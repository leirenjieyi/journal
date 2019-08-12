# alarm() + signal() 的实现方式

    #include <unistd.h>
    unsigned int alarm(unsigned int seconds);
    
    如果参数 seconds=0，则终止一切在等待的alarm，以及之前未决的alarm.

alarm与setitimer共用相同的定时器，调用一个将会影响另一个。

execve调用会保留Alarm,fork调用子进程不会继承alarm.

sleep 有可能是通过SIGALRM实现的，所以最好不要同时调用sleep和alarm.

# setitimer 的实现方式

# create_timer 的实现方式



