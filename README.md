# lab3pt2
//
//  main.cpp
//  lab3pt2
//
//  Created by Monica Andrade on 8/7/17.
//  Copyright © 2017 Monica Andrade. All rights reserved.
//



#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <pwd.h>
#include <sys/stat.h>
#include <unistd.h>
#include <string.h>
#include <dirent.h>

void permission();
typedef int bool;
#define TRUE  1
#define FALSE 0

struct stat buf;

int main(int argc, char *argv[]) {
    
    bool commandline = FALSE; //determine if 0 args passed
    if (argc < 2){commandline=TRUE;}
    struct passwd *passwd;
    passwd = getpwuid(getuid());
    char *file, *dir;
    uid_t uid;  //user id
    gid_t gid;  //group
    uid = getuid();
    gid = getgid();
    DIR *d;
    struct dirent *directory;
    d = opendir(".");
    struct stat buf;
    
    int i,pid=1;
    
    for (i = 1; (i < argc && pid) || (commandline ==TRUE) ; i++) {
        if (!(pid = fork())) {
            
            stat(argv[i], &buf);
        
            if (argc > 1) {
                dir = passwd->pw_dir;
                file = malloc(sizeof(dir) + 1 + sizeof(argv[i]));
                strcat(file, dir);
                strcat(file, "/");
                strcat(file, argv[i]);
                printf("File: %s\nDirectory: %s\n", argv[i], file);
                permission();
            } else {
                if (d) {
                    while ((directory = readdir(d)) != NULL) {
                        dir = passwd->pw_dir;
                        printf("File: %s\n",directory->d_name);
                        printf("Directory: %s/%s\n",dir, directory->d_name);
                        permission();
                    }
                }
            }
        } /* IF CHILD */
        commandline=FALSE;
    } /* FOR LOOP */
    while (wait(NULL) > 0);
    
} /* !Main */

/* PRINT FILE PERMISSIONS*/
void permission() {
    int fileMode;
    
    fileMode = buf.st_mode;
    if (fileMode & S_IRUSR) {
        printf("You have Owner permissions:");
        if (fileMode & S_IREAD) { printf(" Read "); }
        if (fileMode & S_IWRITE) { printf("Write "); }
        if (fileMode & S_IEXEC) { printf("Execute"); }
        printf("\n\n");
    } else if (fileMode & S_IRGRP) {
        printf("You have Group permissions:\n");
        if (fileMode & S_IREAD) { printf(" Read "); }
        if (fileMode & S_IWRITE) { printf("Write "); }
        if (fileMode & S_IEXEC) { printf("Execute"); }
        printf("\n\n");
    } else if (fileMode & S_IROTH) {
        printf("You have General permissions:");
        if (fileMode & S_IREAD) { printf(" Read "); }
        if (fileMode & S_IWRITE) { printf("Write "); }
        if (fileMode & S_IEXEC) { printf("Execute"); }
        printf("\n\n");
    }
}
