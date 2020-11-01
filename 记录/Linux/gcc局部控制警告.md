# gcc局部控制警告

#pragma GCC diagnostic push
#pragma GCC diagnostic ignored "-Wunused-result"
    write(foo, bar, baz);
#pragma GCC diagnostic pop