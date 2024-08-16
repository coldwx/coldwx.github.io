```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <errno.h>

int main(int argc, char *argv[]) {
    char *text = malloc(1);

    for (int i = 1; i < argc; i++) {
        text = realloc(text, strlen(text) + strlen(argv[i]) + 2);
        strcat(text, argv[i]);
        if (i < argc - 1) {
            strcat(text, " ");
        }
    }
    
    char *text_final;
    text_final = malloc(strlen(text) + 2);
    strncpy(text_final, text, strlen(text) + 2);
    strncat(text_final, "\n", strlen("\n") + 2);

    char *file_home = getenv("HOME");
    char *file_name = "/qnote.txt";
    
    char *file_path = malloc(strlen(file_home) + strlen(file_name) + 1);
    strncpy(file_path, file_home, strlen(file_home) + 1);
    strncat(file_path, file_name, strlen(file_name) + 1);

    int fd;
    fd = open(file_path, O_RDWR | O_CREAT, 0644);
    if (fd == -1) {
        fprintf(stderr, "Error '%s': %s\n", file_name, strerror(errno));
        return 1;
    }
    
    close(fd);
    
    int fd_append;
    
    fd_append = open(file_path, O_WRONLY | O_APPEND);
 	if (write(fd_append, text_final, strlen(text_final)) == -1) {
        	perror("Error");
        return 1;
    }	
    
 	close(fd_append);
    
	return 0;
}
```
