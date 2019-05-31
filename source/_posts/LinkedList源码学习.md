---
title: LinkedList源码学习
copyright: true
date: 2019-05-31 08:34:52
tags: JAVA
---

# 简介
LinkedList 是一个实现了List和Deque接口的双向链表， List接口都是一个常规的增删改查操作， 而Deque是的LinkedList具有队列的特性， LinkList不是线程安全的，如果先是的LinkedList变成线程安全的，可以使用
```java
List list=Collections.synchronizedList(new LinkedList(...));
```

<!--more-->

# 源码分析
```java
public class LinkedList<E> extends AbstractSequentialList<E> implements List<E>, Deque<E>, Cloneable, Serializable {
    //一旦变量被transient修饰，变量将不再是对象持久化的一部分，该变量内容在序列化后无法获得访问。
    //transient关键字只能修饰变量，而不能修饰方法和类。注意，本地变量是不能被transient关键字修饰的。变量如果是用户自定义类变量，则该类需要实现Serializable接口。
    //被transient关键字修饰的变量不再能被序列化，一个静态变量不管是否被transient修饰，均不能被序列化
    transient int size;
    transient LinkedList.Node<E> first;
    transient LinkedList.Node<E> last;
    private static final long serialVersionUID = 876323262645176354L;

    public LinkedList() {
        this.size = 0;
    }

    // 根据指定集合添加元素
    public LinkedList(Collection<? extends E> var1) {
        this();
        this.addAll(var1);
    }

    // 将元素插入到队列首部
    private void linkFirst(E var1) {
        LinkedList.Node var2 = this.first;
        LinkedList.Node var3 = new LinkedList.Node((LinkedList.Node)null, var1, var2);
        this.first = var3;
        if (var2 == null) {
            this.last = var3;
        } else {
            var2.prev = var3;
        }

        ++this.size;
        ++this.modCount;
    }

    // 将元素插入到队列尾部
    void linkLast(E var1) {
        LinkedList.Node var2 = this.last;
        LinkedList.Node var3 = new LinkedList.Node(var2, var1, (LinkedList.Node)null);
        this.last = var3;
        if (var2 == null) {
            this.first = var3;
        } else {
            var2.next = var3;
        }

        ++this.size;
        ++this.modCount;
    }

    // 将元素插入到指定位置之前
    void linkBefore(E var1, LinkedList.Node<E> var2) {
        LinkedList.Node var3 = var2.prev;
        LinkedList.Node var4 = new LinkedList.Node(var3, var1, var2);
        var2.prev = var4;
        if (var3 == null) {
            this.first = var4;
        } else {
            var3.next = var4;
        }

        ++this.size;
        ++this.modCount;
    }

    private E unlinkFirst(LinkedList.Node<E> var1) {
        Object var2 = var1.item;
        LinkedList.Node var3 = var1.next;
        var1.item = null;
        var1.next = null;
        this.first = var3;
        if (var3 == null) {
            this.last = null;
        } else {
            var3.prev = null;
        }

        --this.size;
        ++this.modCount;
        return var2;
    }

    private E unlinkLast(LinkedList.Node<E> var1) {
        Object var2 = var1.item;
        LinkedList.Node var3 = var1.prev;
        var1.item = null;
        var1.prev = null;
        this.last = var3;
        if (var3 == null) {
            this.first = null;
        } else {
            var3.next = null;
        }

        --this.size;
        ++this.modCount;
        return var2;
    }

    E unlink(LinkedList.Node<E> var1) {
        Object var2 = var1.item;
        LinkedList.Node var3 = var1.next;
        LinkedList.Node var4 = var1.prev;
        if (var4 == null) {
            this.first = var3;
        } else {
            var4.next = var3;
            var1.prev = null;
        }

        if (var3 == null) {
            this.last = var4;
        } else {
            var3.prev = var4;
            var1.next = null;
        }

        var1.item = null;
        --this.size;
        ++this.modCount;
        return var2;
    }

    public E getFirst() {
        LinkedList.Node var1 = this.first;
        if (var1 == null) {
            throw new NoSuchElementException();
        } else {
            return var1.item;
        }
    }

    public E getLast() {
        LinkedList.Node var1 = this.last;
        if (var1 == null) {
            throw new NoSuchElementException();
        } else {
            return var1.item;
        }
    }

    public E removeFirst() {
        LinkedList.Node var1 = this.first;
        if (var1 == null) {
            throw new NoSuchElementException();
        } else {
            return this.unlinkFirst(var1);
        }
    }

    public E removeLast() {
        LinkedList.Node var1 = this.last;
        if (var1 == null) {
            throw new NoSuchElementException();
        } else {
            return this.unlinkLast(var1);
        }
    }

    public void addFirst(E var1) {
        this.linkFirst(var1);
    }

    public void addLast(E var1) {
        this.linkLast(var1);
    }

    public boolean contains(Object var1) {
        return this.indexOf(var1) != -1;
    }

    public int size() {
        return this.size;
    }

    public boolean add(E var1) {
        this.linkLast(var1);
        return true;
    }

    public boolean remove(Object var1) {
        LinkedList.Node var2;
        if (var1 == null) {
            for(var2 = this.first; var2 != null; var2 = var2.next) {
                if (var2.item == null) {
                    this.unlink(var2);
                    return true;
                }
            }
        } else {
            for(var2 = this.first; var2 != null; var2 = var2.next) {
                if (var1.equals(var2.item)) {
                    this.unlink(var2);
                    return true;
                }
            }
        }

        return false;
    }

    public boolean addAll(Collection<? extends E> var1) {
        return this.addAll(this.size, var1);
    }

    public boolean addAll(int var1, Collection<? extends E> var2) {
        this.checkPositionIndex(var1);
        Object[] var3 = var2.toArray();
        int var4 = var3.length;
        if (var4 == 0) {
            return false;
        } else {
            LinkedList.Node var5;
            LinkedList.Node var6;
            if (var1 == this.size) {
                var6 = null;
                var5 = this.last;
            } else {
                var6 = this.node(var1);
                var5 = var6.prev;
            }

            Object[] var7 = var3;
            int var8 = var3.length;

            for(int var9 = 0; var9 < var8; ++var9) {
                Object var10 = var7[var9];
                LinkedList.Node var12 = new LinkedList.Node(var5, var10, (LinkedList.Node)null);
                if (var5 == null) {
                    this.first = var12;
                } else {
                    var5.next = var12;
                }

                var5 = var12;
            }

            if (var6 == null) {
                this.last = var5;
            } else {
                var5.next = var6;
                var6.prev = var5;
            }

            this.size += var4;
            ++this.modCount;
            return true;
        }
    }

    public void clear() {
        LinkedList.Node var2;
        for(LinkedList.Node var1 = this.first; var1 != null; var1 = var2) {
            var2 = var1.next;
            var1.item = null;
            var1.next = null;
            var1.prev = null;
        }

        this.first = this.last = null;
        this.size = 0;
        ++this.modCount;
    }

    public E get(int var1) {
        this.checkElementIndex(var1);
        return this.node(var1).item;
    }

    public E set(int var1, E var2) {
        this.checkElementIndex(var1);
        LinkedList.Node var3 = this.node(var1);
        Object var4 = var3.item;
        var3.item = var2;
        return var4;
    }

    public void add(int var1, E var2) {
        this.checkPositionIndex(var1);
        if (var1 == this.size) {
            this.linkLast(var2);
        } else {
            this.linkBefore(var2, this.node(var1));
        }

    }

    public E remove(int var1) {
        this.checkElementIndex(var1);
        return this.unlink(this.node(var1));
    }

    private boolean isElementIndex(int var1) {
        return var1 >= 0 && var1 < this.size;
    }

    private boolean isPositionIndex(int var1) {
        return var1 >= 0 && var1 <= this.size;
    }

    private String outOfBoundsMsg(int var1) {
        return "Index: " + var1 + ", Size: " + this.size;
    }

    private void checkElementIndex(int var1) {
        if (!this.isElementIndex(var1)) {
            throw new IndexOutOfBoundsException(this.outOfBoundsMsg(var1));
        }
    }

    private void checkPositionIndex(int var1) {
        if (!this.isPositionIndex(var1)) {
            throw new IndexOutOfBoundsException(this.outOfBoundsMsg(var1));
        }
    }

    LinkedList.Node<E> node(int var1) {
        LinkedList.Node var2;
        int var3;
        if (var1 < this.size >> 1) {
            var2 = this.first;

            for(var3 = 0; var3 < var1; ++var3) {
                var2 = var2.next;
            }

            return var2;
        } else {
            var2 = this.last;

            for(var3 = this.size - 1; var3 > var1; --var3) {
                var2 = var2.prev;
            }

            return var2;
        }
    }

    // 从头开始找
    public int indexOf(Object var1) {
        int var2 = 0;
        LinkedList.Node var3;
        if (var1 == null) {
            for(var3 = this.first; var3 != null; var3 = var3.next) {
                if (var3.item == null) {
                    return var2;
                }

                ++var2;
            }
        } else {
            for(var3 = this.first; var3 != null; var3 = var3.next) {
                if (var1.equals(var3.item)) {
                    return var2;
                }

                ++var2;
            }
        }

        return -1;
    }

    // 从后面开始找
    public int lastIndexOf(Object var1) {
        int var2 = this.size;
        LinkedList.Node var3;
        if (var1 == null) {
            for(var3 = this.last; var3 != null; var3 = var3.prev) {
                --var2;
                if (var3.item == null) {
                    return var2;
                }
            }
        } else {
            for(var3 = this.last; var3 != null; var3 = var3.prev) {
                --var2;
                if (var1.equals(var3.item)) {
                    return var2;
                }
            }
        }

        return -1;
    }

    // 返回头部，为空则返回null
    public E peek() {
        LinkedList.Node var1 = this.first;
        return var1 == null ? null : var1.item;
    }

    public E element() {
        return this.getFirst();
    }

    public E poll() {
        LinkedList.Node var1 = this.first;
        return var1 == null ? null : this.unlinkFirst(var1);
    }

    public E remove() {
        return this.removeFirst();
    }

    public boolean offer(E var1) {
        return this.add(var1);
    }

    public boolean offerFirst(E var1) {
        this.addFirst(var1);
        return true;
    }

    public boolean offerLast(E var1) {
        this.addLast(var1);
        return true;
    }

    public E peekFirst() {
        LinkedList.Node var1 = this.first;
        return var1 == null ? null : var1.item;
    }

    public E peekLast() {
        LinkedList.Node var1 = this.last;
        return var1 == null ? null : var1.item;
    }

    public E pollFirst() {
        LinkedList.Node var1 = this.first;
        return var1 == null ? null : this.unlinkFirst(var1);
    }

    public E pollLast() {
        LinkedList.Node var1 = this.last;
        return var1 == null ? null : this.unlinkLast(var1);
    }

    public void push(E var1) {
        this.addFirst(var1);
    }

    public E pop() {
        return this.removeFirst();
    }

    public boolean removeFirstOccurrence(Object var1) {
        return this.remove(var1);
    }

    public boolean removeLastOccurrence(Object var1) {
        LinkedList.Node var2;
        if (var1 == null) {
            for(var2 = this.last; var2 != null; var2 = var2.prev) {
                if (var2.item == null) {
                    this.unlink(var2);
                    return true;
                }
            }
        } else {
            for(var2 = this.last; var2 != null; var2 = var2.prev) {
                if (var1.equals(var2.item)) {
                    this.unlink(var2);
                    return true;
                }
            }
        }

        return false;
    }

    public ListIterator<E> listIterator(int var1) {
        this.checkPositionIndex(var1);
        return new LinkedList.ListItr(var1);
    }

    public Iterator<E> descendingIterator() {
        return new LinkedList.DescendingIterator();
    }

    private LinkedList<E> superClone() {
        try {
            return (LinkedList)super.clone();
        } catch (CloneNotSupportedException var2) {
            throw new InternalError(var2);
        }
    }

    public Object clone() {
        LinkedList var1 = this.superClone();
        var1.first = var1.last = null;
        var1.size = 0;
        var1.modCount = 0;

        for(LinkedList.Node var2 = this.first; var2 != null; var2 = var2.next) {
            var1.add(var2.item);
        }

        return var1;
    }

    public Object[] toArray() {
        Object[] var1 = new Object[this.size];
        int var2 = 0;

        for(LinkedList.Node var3 = this.first; var3 != null; var3 = var3.next) {
            var1[var2++] = var3.item;
        }

        return var1;
    }

    public <T> T[] toArray(T[] var1) {
        if (var1.length < this.size) {
            var1 = (Object[])((Object[])Array.newInstance(var1.getClass().getComponentType(), this.size));
        }

        int var2 = 0;
        Object[] var3 = var1;

        for(LinkedList.Node var4 = this.first; var4 != null; var4 = var4.next) {
            var3[var2++] = var4.item;
        }

        if (var1.length > this.size) {
            var1[this.size] = null;
        }

        return var1;
    }

    private void writeObject(ObjectOutputStream var1) throws IOException {
        var1.defaultWriteObject();
        var1.writeInt(this.size);

        for(LinkedList.Node var2 = this.first; var2 != null; var2 = var2.next) {
            var1.writeObject(var2.item);
        }

    }

    private void readObject(ObjectInputStream var1) throws IOException, ClassNotFoundException {
        var1.defaultReadObject();
        int var2 = var1.readInt();

        for(int var3 = 0; var3 < var2; ++var3) {
            this.linkLast(var1.readObject());
        }

    }

    public Spliterator<E> spliterator() {
        return new LinkedList.LLSpliterator(this, -1, 0);
    }

    static final class LLSpliterator<E> implements Spliterator<E> {
        static final int BATCH_UNIT = 1024;
        static final int MAX_BATCH = 33554432;
        final LinkedList<E> list;
        LinkedList.Node<E> current;
        int est;
        int expectedModCount;
        int batch;

        LLSpliterator(LinkedList<E> var1, int var2, int var3) {
            this.list = var1;
            this.est = var2;
            this.expectedModCount = var3;
        }

        final int getEst() {
            int var1;
            if ((var1 = this.est) < 0) {
                LinkedList var2;
                if ((var2 = this.list) == null) {
                    var1 = this.est = 0;
                } else {
                    this.expectedModCount = var2.modCount;
                    this.current = var2.first;
                    var1 = this.est = var2.size;
                }
            }

            return var1;
        }

        public long estimateSize() {
            return (long)this.getEst();
        }

        public Spliterator<E> trySplit() {
            int var2 = this.getEst();
            LinkedList.Node var1;
            if (var2 > 1 && (var1 = this.current) != null) {
                int var3 = this.batch + 1024;
                if (var3 > var2) {
                    var3 = var2;
                }

                if (var3 > 33554432) {
                    var3 = 33554432;
                }

                Object[] var4 = new Object[var3];
                int var5 = 0;

                do {
                    var4[var5++] = var1.item;
                } while((var1 = var1.next) != null && var5 < var3);

                this.current = var1;
                this.batch = var5;
                this.est = var2 - var5;
                return Spliterators.spliterator(var4, 0, var5, 16);
            } else {
                return null;
            }
        }

        public void forEachRemaining(Consumer<? super E> var1) {
            if (var1 == null) {
                throw new NullPointerException();
            } else {
                LinkedList.Node var2;
                int var3;
                if ((var3 = this.getEst()) > 0 && (var2 = this.current) != null) {
                    this.current = null;
                    this.est = 0;

                    do {
                        Object var4 = var2.item;
                        var2 = var2.next;
                        var1.accept(var4);
                        if (var2 == null) {
                            break;
                        }

                        --var3;
                    } while(var3 > 0);
                }

                if (this.list.modCount != this.expectedModCount) {
                    throw new ConcurrentModificationException();
                }
            }
        }

        public boolean tryAdvance(Consumer<? super E> var1) {
            if (var1 == null) {
                throw new NullPointerException();
            } else {
                LinkedList.Node var2;
                if (this.getEst() > 0 && (var2 = this.current) != null) {
                    --this.est;
                    Object var3 = var2.item;
                    this.current = var2.next;
                    var1.accept(var3);
                    if (this.list.modCount != this.expectedModCount) {
                        throw new ConcurrentModificationException();
                    } else {
                        return true;
                    }
                } else {
                    return false;
                }
            }
        }

        public int characteristics() {
            return 16464;
        }
    }

    private class DescendingIterator implements Iterator<E> {
        private final LinkedList<E>.ListItr itr;

        private DescendingIterator() {
            this.itr = LinkedList.this.new ListItr(LinkedList.this.size());
        }

        public boolean hasNext() {
            return this.itr.hasPrevious();
        }

        public E next() {
            return this.itr.previous();
        }

        public void remove() {
            this.itr.remove();
        }
    }

    private static class Node<E> {
        E item;
        LinkedList.Node<E> next;
        LinkedList.Node<E> prev;

        Node(LinkedList.Node<E> var1, E var2, LinkedList.Node<E> var3) {
            this.item = var2;
            this.next = var3;
            this.prev = var1;
        }
    }

    private class ListItr implements ListIterator<E> {
        private LinkedList.Node<E> lastReturned;
        private LinkedList.Node<E> next;
        private int nextIndex;
        private int expectedModCount;

        ListItr(int var2) {
            this.expectedModCount = LinkedList.this.modCount;
            this.next = var2 == LinkedList.this.size ? null : LinkedList.this.node(var2);
            this.nextIndex = var2;
        }

        public boolean hasNext() {
            return this.nextIndex < LinkedList.this.size;
        }

        public E next() {
            this.checkForComodification();
            if (!this.hasNext()) {
                throw new NoSuchElementException();
            } else {
                this.lastReturned = this.next;
                this.next = this.next.next;
                ++this.nextIndex;
                return this.lastReturned.item;
            }
        }

        public boolean hasPrevious() {
            return this.nextIndex > 0;
        }

        public E previous() {
            this.checkForComodification();
            if (!this.hasPrevious()) {
                throw new NoSuchElementException();
            } else {
                this.lastReturned = this.next = this.next == null ? LinkedList.this.last : this.next.prev;
                --this.nextIndex;
                return this.lastReturned.item;
            }
        }

        public int nextIndex() {
            return this.nextIndex;
        }

        public int previousIndex() {
            return this.nextIndex - 1;
        }

        public void remove() {
            this.checkForComodification();
            if (this.lastReturned == null) {
                throw new IllegalStateException();
            } else {
                LinkedList.Node var1 = this.lastReturned.next;
                LinkedList.this.unlink(this.lastReturned);
                if (this.next == this.lastReturned) {
                    this.next = var1;
                } else {
                    --this.nextIndex;
                }

                this.lastReturned = null;
                ++this.expectedModCount;
            }
        }

        public void set(E var1) {
            if (this.lastReturned == null) {
                throw new IllegalStateException();
            } else {
                this.checkForComodification();
                this.lastReturned.item = var1;
            }
        }

        public void add(E var1) {
            this.checkForComodification();
            this.lastReturned = null;
            if (this.next == null) {
                LinkedList.this.linkLast(var1);
            } else {
                LinkedList.this.linkBefore(var1, this.next);
            }

            ++this.nextIndex;
            ++this.expectedModCount;
        }

        public void forEachRemaining(Consumer<? super E> var1) {
            Objects.requireNonNull(var1);

            while(LinkedList.this.modCount == this.expectedModCount && this.nextIndex < LinkedList.this.size) {
                var1.accept(this.next.item);
                this.lastReturned = this.next;
                this.next = this.next.next;
                ++this.nextIndex;
            }

            this.checkForComodification();
        }

        final void checkForComodification() {
            if (LinkedList.this.modCount != this.expectedModCount) {
                throw new ConcurrentModificationException();
            }
        }
    }
}

```

[JavaGuide的demo](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/LinkedList.md)
```java
package list;

import java.util.Iterator;
import java.util.LinkedList;

public class LinkedListDemo {
    public static void main(String[] srgs) {
        //创建存放int类型的linkedList
        LinkedList<Integer> linkedList = new LinkedList<>();
        /************************** linkedList的基本操作 ************************/
        linkedList.addFirst(0); // 添加元素到列表开头
        linkedList.add(1); // 在列表结尾添加元素
        linkedList.add(2, 2); // 在指定位置添加元素
        linkedList.addLast(3); // 添加元素到列表结尾

        System.out.println("LinkedList（直接输出的）: " + linkedList);

        System.out.println("getFirst()获得第一个元素: " + linkedList.getFirst()); // 返回此列表的第一个元素
        System.out.println("getLast()获得第最后一个元素: " + linkedList.getLast()); // 返回此列表的最后一个元素
        System.out.println("removeFirst()删除第一个元素并返回: " + linkedList.removeFirst()); // 移除并返回此列表的第一个元素
        System.out.println("removeLast()删除最后一个元素并返回: " + linkedList.removeLast()); // 移除并返回此列表的最后一个元素
        System.out.println("After remove:" + linkedList);
        System.out.println("contains()方法判断列表是否包含1这个元素:" + linkedList.contains(1)); // 判断此列表包含指定元素，如果是，则返回true
        System.out.println("该linkedList的大小 : " + linkedList.size()); // 返回此列表的元素个数

        /************************** 位置访问操作 ************************/
        System.out.println("-----------------------------------------");
        linkedList.set(1, 3); // 将此列表中指定位置的元素替换为指定的元素
        System.out.println("After set(1, 3):" + linkedList);
        System.out.println("get(1)获得指定位置（这里为1）的元素: " + linkedList.get(1)); // 返回此列表中指定位置处的元素

        /************************** Search操作 ************************/
        System.out.println("-----------------------------------------");
        linkedList.add(3);
        System.out.println("indexOf(3): " + linkedList.indexOf(3)); // 返回此列表中首次出现的指定元素的索引
        System.out.println("lastIndexOf(3): " + linkedList.lastIndexOf(3));// 返回此列表中最后出现的指定元素的索引

        /************************** Queue操作 ************************/
        System.out.println("-----------------------------------------");
        System.out.println("peek(): " + linkedList.peek()); // 获取但不移除此列表的头
        System.out.println("element(): " + linkedList.element()); // 获取但不移除此列表的头
        linkedList.poll(); // 获取并移除此列表的头
        System.out.println("After poll():" + linkedList);
        linkedList.remove();
        System.out.println("After remove():" + linkedList); // 获取并移除此列表的头
        linkedList.offer(4);
        System.out.println("After offer(4):" + linkedList); // 将指定元素添加到此列表的末尾

        /************************** Deque操作 ************************/
        System.out.println("-----------------------------------------");
        linkedList.offerFirst(2); // 在此列表的开头插入指定的元素
        System.out.println("After offerFirst(2):" + linkedList);
        linkedList.offerLast(5); // 在此列表末尾插入指定的元素
        System.out.println("After offerLast(5):" + linkedList);
        System.out.println("peekFirst(): " + linkedList.peekFirst()); // 获取但不移除此列表的第一个元素
        System.out.println("peekLast(): " + linkedList.peekLast()); // 获取但不移除此列表的第一个元素
        linkedList.pollFirst(); // 获取并移除此列表的第一个元素
        System.out.println("After pollFirst():" + linkedList);
        linkedList.pollLast(); // 获取并移除此列表的最后一个元素
        System.out.println("After pollLast():" + linkedList);
        linkedList.push(2); // 将元素推入此列表所表示的堆栈（插入到列表的头）
        System.out.println("After push(2):" + linkedList);
        linkedList.pop(); // 从此列表所表示的堆栈处弹出一个元素（获取并移除列表第一个元素）
        System.out.println("After pop():" + linkedList);
        linkedList.add(3);
        linkedList.removeFirstOccurrence(3); // 从此列表中移除第一次出现的指定元素（从头部到尾部遍历列表）
        System.out.println("After removeFirstOccurrence(3):" + linkedList);
        linkedList.removeLastOccurrence(3); // 从此列表中移除最后一次出现的指定元素（从尾部到头部遍历列表）
        System.out.println("After removeFirstOccurrence(3):" + linkedList);

        /************************** 遍历操作 ************************/
        System.out.println("-----------------------------------------");
        linkedList.clear();
        for (int i = 0; i < 100000; i++) {
            linkedList.add(i);
        }
        // 迭代器遍历
        long start = System.currentTimeMillis();
        Iterator<Integer> iterator = linkedList.iterator();
        while (iterator.hasNext()) {
            iterator.next();
        }
        long end = System.currentTimeMillis();
        System.out.println("Iterator：" + (end - start) + " ms");

        // 顺序遍历(随机遍历)
        start = System.currentTimeMillis();
        for (int i = 0; i < linkedList.size(); i++) {
            linkedList.get(i);
        }
        end = System.currentTimeMillis();
        System.out.println("for：" + (end - start) + " ms");

        // 另一种for循环遍历
        start = System.currentTimeMillis();
        for (Integer i : linkedList)
            ;
        end = System.currentTimeMillis();
        System.out.println("for2：" + (end - start) + " ms");

        // 通过pollFirst()或pollLast()来遍历LinkedList
        LinkedList<Integer> temp1 = new LinkedList<>();
        temp1.addAll(linkedList);
        start = System.currentTimeMillis();
        while (temp1.size() != 0) {
            temp1.pollFirst();
        }
        end = System.currentTimeMillis();
        System.out.println("pollFirst()或pollLast()：" + (end - start) + " ms");

        // 通过removeFirst()或removeLast()来遍历LinkedList
        LinkedList<Integer> temp2 = new LinkedList<>();
        temp2.addAll(linkedList);
        start = System.currentTimeMillis();
        while (temp2.size() != 0) {
            temp2.removeFirst();
        }
        end = System.currentTimeMillis();
        System.out.println("removeFirst()或removeLast()：" + (end - start) + " ms");
    }
}
```
