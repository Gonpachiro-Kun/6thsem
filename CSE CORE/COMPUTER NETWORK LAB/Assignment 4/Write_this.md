### 1. Write a TCP client server program to send hello message from client  to server. Server reverses the message and sends back to client. Client print the reversed message. 

Server Code -

```
#include<string.h>
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<sys/types.h>
#define MAXLINE 20
#define SERV_PORT 5777
main(int argc,char *argv)
{
int i,j;
ssize_t n;
char line[MAXLINE],revline[MAXLINE];
int listenfd,connfd,clilen;
struct sockaddr_in servaddr,cliaddr;
listenfd=socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(SERV_PORT);
bind(listenfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
listen(listenfd,1);
for( ; ; )
{
clilen=sizeof(cliaddr);
connfd=accept(listenfd,(struct sockaddr*)&cliaddr,&clilen);
printf("Connect to client.\n");

while(1)
{
if((n=read(connfd,line,MAXLINE))==0)
break;
printf("Data Received.\n");
printf("Data: %s",line);
line[n-1]='\0';
j=0;
for(i=n-2;i>=0;i--)
revline[j++]=line[i];
revline[j]='\0';
write(connfd,revline,n); //This line writes the reversed data (revline) back to the client using the write() system call.
}
}
}
```
Client Code -

```
#include<string.h>
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<sys/types.h>
#define MAXLINE 20
#define SERV_PORT 5777
main(int argc,char *argv)
{
char sendline[MAXLINE],revline[MAXLINE];
int sockfd;
struct sockaddr_in servaddr;
sockfd=socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=ntohs(SERV_PORT);
connect(sockfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
printf("\n Enter the data to be send: ");
while(fgets(sendline,MAXLINE,stdin)!=NULL)
{
write(sockfd,sendline,strlen(sendline));
printf("\n Line send");
read(sockfd,revline,MAXLINE);
printf("\n Reverse of the given sentence is : %s",revline);
printf("\n");
printf("\n Enter the data to be send: ");
}
exit(0);
}
```

Output -

```
 Enter the data to be send: this is a test

 Line send
 Reverse of the given sentence is : tset a si siht
```

### 2. Write a UDP client server message where the client sends a file to the server. The server reads the content of the file and prints it on the console. 

Server Code -

```
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#define SERV_PORT 6349
main(int argc,char **argv)
{
char filename[80],recvline[80];
FILE *fp;
struct sockaddr_in servaddr,cliaddr;
int clilen,sockfd;
sockfd=socket(AF_INET,SOCK_DGRAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(SERV_PORT);
bind(sockfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
clilen=sizeof(cliaddr);
recvfrom(sockfd,filename,80,0,(struct sockaddr*)&cliaddr,&clilen);
printf("\nData in the file is: \n ");
fp=fopen(filename,"r");
while(fgets(recvline,80,fp)!=NULL)
{
printf("\n %s\n ",recvline);
}
fclose(fp);
}
```
Client Code -
```
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<unistd.h>
#define SERV_PORT 6349
main(int argc,char **argv)
{
char filename[80];
int sockfd;
struct sockaddr_in servaddr;
sockfd=socket(AF_INET,SOCK_DGRAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(SERV_PORT);
inet_pton(AF_INET,argv[1],&servaddr.sin_addr);
printf("Enter the file name: ");
scanf("%s",filename);
sendto(sockfd,filename,strlen(filename),0,(struct sockaddr*)&servaddr,sizeof(servaddr));
}
```

Output -

```
./a.out 8080
Enter the file name: a.txt

Data in the file is:

this is a test
```

### 3. Write a TCP client server message where the client sends a file to the server. The server reads the content of the file and prints it on the console. 

Server Code -

```
#include<stdio.h>
#include<unistd.h>
#include<string.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<sys/types.h>
#define SERV_PORT 5576
main(int argc,char **argv)
{
int i,j;
ssize_t n;
FILE *fp;
char s[80],f[80];
struct sockaddr_in servaddr,cliaddr;
int listenfd,connfd,clilen;
listenfd=socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(SERV_PORT);
bind(listenfd,(struct sockaddr *)&servaddr,sizeof(servaddr));
listen(listenfd,1);
clilen=sizeof(cliaddr);
connfd=accept(listenfd,(struct sockaddr*)&cliaddr,&clilen);
printf("\nClient connected. ");
read(connfd,f,80);
fp=fopen(f,"r");
printf("\nName of the file: %s",f);
while(fgets(s,80,fp)!=NULL)
{
printf("\nContent of the file: %s",s);
write(connfd,s,sizeof(s));
}
}
```
Client Code -

```
#include<stdio.h>
#include<unistd.h>
#include<string.h>
#include<sys/socket.h>
#include<netinet/in.h>
#include<sys/types.h>
#define SERV_PORT 5576
main(int argc,char **argv)
{
int i,j;
ssize_t n;
char filename[80],recvline[80];
struct sockaddr_in servaddr;
int sockfd;
sockfd=socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(SERV_PORT);
inet_pton(AF_INET,argv[1],&servaddr.sin_addr);
connect(sockfd,(struct sockaddr*)&servaddr,sizeof(servaddr));
printf("Enter the file name: ");
scanf("%s",filename);
write(sockfd,filename,sizeof(filename));
printf("\n Data from server: \n");
while(read(sockfd,recvline,80)!=0)
{
fputs(recvline,stdout);
}
}
```

Output -

```
./a.out 8080
Enter the file name: a.txt

Data from server:
this is a test
```

### 4. Write a  TCP client server program where client will send an input file to the server. Inside that input file a sentence will be there. After receiving the file server reverse individual word of that sentence and write it into another file and reply back that new file to the client. Client will read the file and print the content of the file on the console.

Server Code -

```

```
Client Code -

```

```

Output -

```
🖕
```
