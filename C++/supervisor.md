1. ``read(pipe_fd[0], r_buf, 4)``

   pipe_fd[0]是管道文件，若系统打开文件表中有pipe_fd[0], 且文件指针处没有东西，那么就等待。

