# 读取文本文件并按行分割

> 原文：<https://dev.to/saitoatsushi/reading-text-file-and-split-by-line-78c>

c 语言中的文本处理有很多麻烦，即使只是编写逐行读取的程序也很费时间。我看过[篇试图实现它的文章](http://fa11enprince.hatenablog.com/entry/2016/01/26/025647)。

我有编写聊天 CGI 的经验，包括文本处理。

只需通过 fread 一次性读取整个文件。

[![reading text by line](img/6f1612b9e229ee689dbe27ede20259b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SxOeif4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p5xabmucg62h1d2jvqom.png)

并将 return 替换为 null。它显然是一个行数组。

[![replace return to null](img/66d9d70635dd571db36bcaa5c2c3e604.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--thaRMzlF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ov6jt8zv69uolunv2ud.png)

实现如上。

```
// whole.h
char** whole(const char* const filename);
void freewhole(char** m); 
```

Enter fullscreen mode Exit fullscreen mode

```
// whole.c
#include <stdio.h>
#include <stdlib.h> 
static long int filesize(FILE* fp) {
  fseek(fp, 0, SEEK_END);
  long int size = ftell(fp);
  fseek(fp, 0, SEEK_SET);
  return size;
}

static int countline(char* block) {
  int line = 0;

  for(int i=0, flag=0; block[i]; i++)
    switch(block[i]) {
    case '\r': line++; flag=1; break;
    case '\n': if(flag) flag = 0; else line++; break;
    default: flag = 0;
    }

  return line+2;
}

static char* nextline(char* str) {
  char* n;
  for(n=str; *n!='\n' && *n!='\r' && *n!='\0'; n++);
  switch(*n) {
  case '\0': return NULL;
  case '\r': *n++='\0'; if(*n=='\n') n++; if(*n=='\0') return NULL; break;
  case '\n': *n++='\0'; if(*n=='\0') return NULL; break;
  }
  return n;
}

static char* readall(const char* const filename) {
  FILE* fp = fopen(filename, "rb");
  if(fp==NULL) {perror(NULL); exit(EXIT_FAILURE); }
  int size = filesize(fp);
  char* block = malloc(size+1);
  if(block==NULL) {perror(NULL); exit(EXIT_FAILURE); }
  int read_size = fread(block, 1, size, fp);
  fclose(fp);
  block[read_size]='\0';
  return block;
}

static char** split(char* block) {
  int linelength=countline(block);
  char** lines = malloc(linelength*sizeof(char*));
  if(lines==NULL) {perror(NULL); exit(EXIT_FAILURE); }
  char** p;
  for(p=lines; block!=NULL; block=nextline(block)) *p++=block;
  *p=NULL;
  return lines;
}

char** whole(const char* const filename) {
  char* block = readall(filename);
  return split(block);
}

void freewhole(char** m) {
  free(m[0]);
  free(m);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用示例。

```
// example.c
#include <stdio.h>
#include "whole.h"

int main(void) {
  char** lines = whole("test.txt");

  for(int i=0; lines[i]; i++) printf("%d %s\n", i, lines[i]);

  freewhole(lines);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode