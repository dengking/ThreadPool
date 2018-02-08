# 20180130
第一次看源代码的时候，发现了condition_variable，又联想到上周末看APUE的线程这一章的时候，恰好包含了condition_variable；当时我在看APUE的condition_variable小节的时候，没有搞得很清楚，然后这一晚上花了一些时间查阅了一些资料终于搞清楚了；刚刚又重新看了
# 20180131
刚刚看了这个线程池的实现，如果用生产者和消费者模型来理解作者的设计思路的话，这个线程池充当的是消费者的角色，线程池中的线程负责执行用户放入到队列中的函数。

关于线程参考一下这篇文章：http://lib.csdn.net/article/cplusplus/53612?knId=1165

# 20180201

现在阅读代码，发现其实有很多地方都没有搞得很清楚，下面这篇文章介绍了本库的实现，可以参考之：

http://blog.csdn.net/GavinGreenson/article/details/72770818

## 对main.cpp的理解

`ThreadPool pool(4);`

当使用上面的语句说明一个线程池对象后，此时线程池中的线程们语句开始运行了。由于此时队列为空，因此这些线程都被阻塞了。

`std::vector< std::future<int> > results;`

results的元素类型是std::future<int>，std::future<int>用于获取异步任务，也就是线程的执行结果。由于是线程池，所以使用了容器vector。

```c++
for(int i = 0; i < 8; ++i) {
        results.emplace_back(
            pool.enqueue([i] {
                std::cout << "hello " << i << std::endl;
                std::this_thread::sleep_for(std::chrono::seconds(1));
                std::cout << "world " << i << std::endl;
                return i*i;
            })
        );
    }
```

这个for循环所执行的是向线程池的队列中放入8个任务，并且为每个任务指定获取任务执行结果的future。一旦向线程池的队列中放入任务后，线程池中的线程将读取任务并执行任务。

