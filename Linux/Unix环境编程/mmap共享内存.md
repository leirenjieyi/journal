在用mmap映射一个已存在的文件时，mmap的大小不能超过这个文件的大小。解决办法是先用

 `int stat(const char *restrict path, struct stat *restrict buf);` 
 
 获取文件的大小，如果文件大小小于要映射的大小，则需要先扩展文件的大小。采用 
 
 `off_t lseek(int fd, off_t offset, int whence);`
 
  函数扩展文件，扩展完成后，文件大小不会立即改变，需要做一次io操作，用 
  
  `ssize_t write(int fildes, const void *buf, size_t nbyte);` 
  
  随便写入点什么。这时文件大小就改变了，然后再吃用mmap进行映射就可以了。