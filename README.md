# nodexss
Simple and vulnerable NodeJS app prone to Cross-Site Scripting (XSS)

## Usage:

```
git clone https://github.com/defensahacker/nodexss.git
docker build -t defensahacker/nodexss:1.3 --no-cache .
docker run --rm -p 8080:8080 -d defensahacker/nodexss:1.3
```

And run the webbrowser with:
```
http://localhost:8080/?name=<b>world</b><script>alert(document.cookie);</script>
```

There is a patched version using xss library here:
```
http://localhost:8080/patched?name=<b>world</b><script>alert(document.cookie);</script>
```

## Remarks:

1) Take into account that some web browser like Chrome detect and mitigate part of XSS, so open the developer console to check any error.

2) Also consider that:
```
<b>world</b><script>alert(document.cookie);</script>
```
is equal to
```
%3Cb%3Eworld%3C/b%3E%3Cscript%3Ealert(document.cookie);%3C/script%3E
```
using URL-encoding.

Let me give you some examples using *Curl*:
Vulnerable version:
```
$ curl "http://localhost:8080/?name=<b>world</b><script>alert(document.cookie);</script>"
Hello <b>world</b><script>alert(document.cookie);</script>

$ curl "http://localhost:8080/?name=%3Cb%3Eworld%3C/b%3E%3Cscript%3Ealert(document.cookie);%3C/script%3E"
Hello <b>world</b><script>alert(document.cookie);</script>
```

Patched version:
```
$ curl "http://localhost:8080/patched?name=<b>world</b><script>alert(document.cookie);</script>"
Hello &lt;b&gt;world&lt;/b&gt;&lt;script&gt;alert(document.cookie);&lt;/script&gt;

$ curl "http://localhost:8080/patched?name=%3Cb%3Eworld%3C/b%3E%3Cscript%3Ealert(document.cookie);%3C/script%3E"
Hello &lt;b&gt;world&lt;/b&gt;&lt;script&gt;alert(document.cookie);&lt;/script&gt;
```
