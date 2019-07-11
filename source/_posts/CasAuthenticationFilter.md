---
title: Spring Secutiry 登陆源码分析
copyright: true
date: 2019-07-11 09:18:30
tags: Spring Secutiry, Cas
---

# CasAuthenticationFilter 简介|（版本：3.0.7）

处理**Cas service ticket**


## Service Tickets
一个服务票据是由一串加密的票证字符串组成， 服务票据是用户的浏览器通过Cas Server 认证后， 通过HTTP Redirect 到资源服务器中， 而服务票据是通过其中的请求参数中获取到的。

过滤器监视着Service URL 以致于它可以接收到服务票据并进行处理，**The CAS server knows which service URL to use via the ServiceProperties.getService()** method。

Processing the **service ticket** involves creating a **UsernamePasswordAuthenticationToken(the principal and the opaque ticket string as the credentials)** which uses **CAS_STATEFUL_IDENTIFIER** for the principal and the opaque ticket string as the credentials.**(通过服务票据生成 UsernamePasswordAuthenticationToken)**

The configured **AuthenticationManager** is expected to provide a provider that can recognise UsernamePasswordAuthenticationTokens containing this special principal name, and process them accordingly by validation with the CAS server.**（通过AuthenticationManager 依据Cas server 来处理识别该证书）**

<!--more-->

By configuring a shared ProxyGrantingTicketStorage between the TicketValidator and the CasAuthenticationFilter one can have the CasAuthenticationFilter handle the proxying requirements for CAS. In addition, the URI endpoint for the proxying would also need to be configured (i.e. the part after protocol, hostname, and port).（代理相关）

# Spring Secutiry 登陆基本流程（源码分析）


```java
        /**
        * AbstractAuthenticationProcessingFilter
        */
        public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest)req;
        HttpServletResponse response = (HttpServletResponse)res;
        /*
        * Invokes the requiresAuthentication method to determine whether the request is for
        * authentication and should be handled by this filter.（如果是需要认证的话， 再进入此过滤类）
        */
        if (!this.requiresAuthentication(request, response)) {
            chain.doFilter(request, response);
        } else {
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Request is to process authentication");
            }

            Authentication authResult;
            try {
                /*
                * If it is an authentication request, the attemptAuthentication will be invoked to perform the authentication. (调用了子类的方法)
                */
                authResult = this.attemptAuthentication(request, response);
                if (authResult == null) {
                    return;
                }

                // // 最终认证成功后，会处理一些与session相关的方法（比如将认证信息存到session等操作）
                this.sessionStrategy.onAuthentication(authResult, request, response);
            } catch (InternalAuthenticationServiceException var8) {
                this.logger.error("An internal error occurred while trying to authenticate the user.", var8);
                this.unsuccessfulAuthentication(request, response, var8);
                return;
            } catch (AuthenticationException var9) {
                this.unsuccessfulAuthentication(request, response, var9);
                return;
            }

            if (this.continueChainBeforeSuccessfulAuthentication) {
                chain.doFilter(request, response);
            }
            /*
             * 最终认证成功后的相关回调方法，主要将当前的认证信息放到SecurityContextHolder中
             * 并调用成功处理器做相应的操作。
             */
            this.successfulAuthentication(request, response, chain, authResult);
        }
    }

    /**
    * CasAuthenticationFilter
    *
    * Performs actual authentication.
    * 1.Return a populated authentication token for the authenticated user, indicating
    * successful authentication
    * 2.Return null, indicating that the authentication process is still in progress. Before
    * returning, the implementation should perform any additional work required to complete the * process.
    * 3.Throw an AuthenticationException if the authentication process fails
    */
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException, IOException {
        if (this.proxyReceptorRequest(request)) {
            this.logger.debug("Responding to proxy receptor request");
            CommonUtils.readAndRespondToProxyReceptorRequest(request, response, this.proxyGrantingTicketStorage);
            return null;
        } else {
            boolean serviceTicketRequest = this.serviceTicketRequest(request, response);
            String username = serviceTicketRequest ? "_cas_stateful_" : "_cas_stateless_";
            String password = this.obtainArtifact(request);
            if (password == null) {
                this.logger.debug("Failed to obtain an artifact (cas ticket)");
                password = "";
            }

            UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
            authRequest.setDetails(this.authenticationDetailsSource.buildDetails(request));

            // 调用Authentication（ProviderManager）
            return this.getAuthenticationManager().authenticate(authRequest);
        }
    }

    /*
    * UsernamePasswordAuthenticationToken
    *
    * 保存用户信息， 并设置授权为false
    */
    public UsernamePasswordAuthenticationToken(Object principal, Object credentials) {
    super((Collection)null);
    this.principal = principal;
    this.credentials = credentials;
    this.setAuthenticated(false);
    }

    /*
    * AuthenticationManager -> ProviderManager
    *
    * 进行校验工作  
    */
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        Class<? extends Authentication> toTest = authentication.getClass();
        AuthenticationException lastException = null;
        Authentication result = null;
        boolean debug = logger.isDebugEnabled();
        Iterator var6 = this.getProviders().iterator();

        while(var6.hasNext()) {
            AuthenticationProvider provider = (AuthenticationProvider)var6.next();
            if (provider.supports(toTest)) {
                if (debug) {
                    logger.debug("Authentication attempt using " + provider.getClass().getName());
                }

                try {
                    result = provider.authenticate(authentication);
                    // 如果有处理器成功处理， 则退出
                    if (result != null) {
                        this.copyDetails(authentication, result);
                        break;
                    }
                } catch (AccountStatusException var11) {
                    this.prepareException(var11, authentication);
                    throw var11;
                } catch (InternalAuthenticationServiceException var12) {
                    this.prepareException(var12, authentication);
                    throw var12;
                } catch (AuthenticationException var13) {
                    lastException = var13;
                }
            }
        }

        // 如果没有处理器能够处理， 则调用父类的
        if (result == null && this.parent != null) {
            try {
                result = this.parent.authenticate(authentication);
            } catch (ProviderNotFoundException var9) {
                ;
            } catch (AuthenticationException var10) {
                lastException = var10;
            }
        }

        if (result != null) {
            if (this.eraseCredentialsAfterAuthentication && result instanceof CredentialsContainer) {
                ((CredentialsContainer)result).eraseCredentials();
            }

            this.eventPublisher.publishAuthenticationSuccess(result);
            return result;
        } else {
            if (lastException == null) {
                lastException = new ProviderNotFoundException(this.messages.getMessage("ProviderManager.providerNotFound", new Object[]{toTest.getName()}, "No AuthenticationProvider found for {0}"));
            }

            this.prepareException((AuthenticationException)lastException, authentication);
            throw lastException;
        }
    }

    /*
    * AuthenticationProvider -> AbstractUserDetailsAuthenticationProvider
    */
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
    Assert.isInstanceOf(UsernamePasswordAuthenticationToken.class, authentication, this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.onlySupports", "Only UsernamePasswordAuthenticationToken is supported"));
    String username = authentication.getPrincipal() == null ? "NONE_PROVIDED" : authentication.getName();
    boolean cacheWasUsed = true;
    UserDetails user = this.userCache.getUserFromCache(username);
    if (user == null) {
        cacheWasUsed = false;

        try {
            user = this.retrieveUser(username, (UsernamePasswordAuthenticationToken)authentication);
        } catch (UsernameNotFoundException var6) {
            this.logger.debug("User '" + username + "' not found");
            if (this.hideUserNotFoundExceptions) {
                throw new BadCredentialsException(this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
            }

            throw var6;
        }

        Assert.notNull(user, "retrieveUser returned null - a violation of the interface contract");
    }

    /**
    * DaoAuthenticationProvider
    */
    protected final UserDetails retrieveUser(String username, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
       UserDetails loadedUser;
       try {
           loadedUser = this.getUserDetailsService().loadUserByUsername(username);
       } catch (UsernameNotFoundException var6) {
           if (authentication.getCredentials() != null) {
               String presentedPassword = authentication.getCredentials().toString();
               this.passwordEncoder.isPasswordValid(this.userNotFoundEncodedPassword, presentedPassword, (Object)null);
           }

           throw var6;
       } catch (Exception var7) {
           throw new InternalAuthenticationServiceException(var7.getMessage(), var7);
       }

       if (loadedUser == null) {
           throw new InternalAuthenticationServiceException("UserDetailsService returned null, which is an interface contract violation");
       } else {
           return loadedUser;
       }
   }

   /*
   * DaoUserDetailsService 自定义
   */
   @Override
public UserDetails loadUserByUsername(final String username) throws UsernameNotFoundException {

    try {
      final User user = userRepository.loadUserByUsername(username);
        if (user == null) {
            throw new Exception("用户名或密码错误");
        }
        MyUserPrincipal myPrincipal = new MyUserPrincipal(user.getIsadmin(), user.getRealname(),user.getId(), user.getPassword(), MyUserPrincipal.getAuthorities(user.getRoles()));
        return myPrincipal;
    } catch (final Exception e) {
        throw new RuntimeException(e);
    }
}
```


# 官方文档
- [CasAuthenticationFilter 官方文档](https://docs.spring.io/autorepo/docs/spring-security/3.0.7.RELEASE/apidocs/org/springframework/security/cas/web/CasAuthenticationFilter.html)
- [AbstractAuthenticationProcessingFilter 官方文档](https://docs.spring.io/spring-security/site/docs/4.2.12.RELEASE/apidocs/org/springframework/security/web/authentication/AbstractAuthenticationProcessingFilter.html)
- [简书上的相关解释](https://zhuanlan.zhihu.com/p/35408527)
