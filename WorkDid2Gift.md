# Work did 2 earn the gift - 

This is the steps followed to support https request:

First we run the kotlin server and if we use url to do a request:

```bash
curl http://127.0.0.1:8080
```

And it return:

```bash
HTTP/1.1 400
Content-Type: text/plain;charset=UTF-8
Connection: close
```

It's normal because the server didn't configurate to answer.

Now we gonna use https:

