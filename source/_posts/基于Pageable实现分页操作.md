---
title: 基于pageable实现分页操作
date: 2019-07-10 11:49:23
tags: Spring Data Jpa
---

# 简介
- Pageable 是Spring Data库中定义的一个接口，该接口是所有分页相关信息的一个抽象，通过该接口，我们可以得到和分页相关所有信息（例如pageNumber、pageSize等），这样，Jpa就能够通过pageable参数来得到一个带分页信息的Sql语句。
- Page类也是Spring Data提供的一个接口，该接口表示一部分数据的集合以及其相关的下一部分数据、数据总数等相关信息，通过该接口，我们可以得到数据的总体信息（数据总数、总页数...）以及当前数据的信息（当前数据的集合、当前页数等）


## 通过参数获取Pageable
```java
@RequestMapping(value = "/params", method=RequestMethod.GET)
public Page<Blog> getEntryByParams(@RequestParam(value = "page", defaultValue = "0") Integer page,
        @RequestParam(value = "size", defaultValue = "15") Integer size) {
    Sort sort = new Sort(Direction.DESC, "id");
    Pageable pageable = new PageRequest(page, size, sort);
    return blogRepository.findAll(pageable);
}
```

## 直接获取Pageable 对象
当Spring发现这个参数时，Spring会自动的根据request的参数来组装该pageable对象，Spring支持的request参数如下：

- page，第几页，从0开始，默认为第0页
- size，每一页的大小，默认为20
- sort，排序相关的信息，以property,property(,ASC|DESC)的方式组织，例如sort=firstname&sort=lastname,desc表示在按firstname正序排列基础上按lastname倒序排列

```java
@RequestMapping(value = "", method=RequestMethod.GET)
public Page<Blog> getEntryByPageable(@PageableDefault(value = 15, sort = { "id" }, direction = Sort.Direction.DESC)
    Pageable pageable) {
    return blogRepository.findAll(pageable);
}
```

# 参考
- [Spring Data Jpa: 分页和排序](https://blog.csdn.net/u011848397/article/details/52151673)
- [Pageable 前台传参](https://www.jianshu.com/p/67249c7b81d4)
