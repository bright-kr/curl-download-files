# 파일 다운로드를 위한 cURL 가이드

[![Promo](https://github.com/bright-kr/LinkedIn-Scraper/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/) 

이 가이드에서는 다음 내용을 확인하실 수 있습니다:

- [기본 cURL 파일 다운로드 문법](#basic-curl-download-file-syntax)
- [cURL로 파일을 다운로드할 때 더 복잡한 시나리오를 처리하는 방법](#using-curl-to-download-a-file-advanced-options)
- [여러 파일을 한 번에 다운로드하는 방법](#how-to-download-multiple-files-with-curl)
- [cURL을 효과적으로 사용하기 위한 몇 가지 모범 사례](#best-practices-when-downloading-files-with-curl)
- [cURL과 Wget 간의 빠른 비교](#curl-vs-wget-for-downloading-files)

바로 시작해 보겠습니다!

---

## Basic cURL Download File Syntax

아래는 가장 기본적인 cURL 파일 다운로드 문법입니다:

```powershell
curl -O <file_url>
```

> 💡 **중요:**
> Windows에서는 `curl`이 Windows PowerShell에서 [`Invoke-WebRequest`](https://github.com/bright-kr/Invoke-web-request-proxy)의 별칭(alias)입니다. 충돌을 피하려면 `curl`을 `curl.exe`로 바꾸어 사용하십시오.

[`-O`](https://curl.se/docs/manpage.html#-O) 및 `--remote-name` 플래그는 cURL이 다운로드한 파일을 원래 이름으로 저장하도록 지시합니다:

```powershell
curl --remote-name <file_url>
```  
예를 들어, 다음과 같은 파일 다운로드 cURL 명령을 고려해 보십시오:

```powershell
curl -O "https://i.imgur.com/CSRiAeN.jpg"
```

그러면 아래와 같이 다운로드 진행률 표시줄이 포함된 출력이 생성됩니다:

```
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   155k      0 --:--:-- --:--:-- --:--:--  158k
```

진행률 표시줄이 100%에 도달하면, cURL 명령을 실행한 폴더에 `CSRiAeN.jpg`라는 파일이 나타납니다:

![The downloaded CSRiAeN.jpg file in the folder where cURL was launched](https://github.com/bright-kr/curl-download-files/blob/main/image-37.png)

cURL 및 해당 옵션에 대한 자세한 내용은 [cURL 가이드를 읽어보십시오](https://brightdata.co.kr/blog/web-data/what-is-curl).

## Using cURL to Download a File: Advanced Options

추가 옵션 몇 가지를 알아보겠습니다.

### Change Downloaded File Name

기본적으로 `-O` 옵션은 다운로드한 파일을 원래 이름으로 저장합니다. URL의 원격 파일에 이름이 포함되어 있지 않으면, cURL은 확장자가 없는 `curl_response`라는 파일을 생성합니다:

![The default curl_response file in the folder where cURL was launched](https://github.com/bright-kr/curl-download-files/blob/main/image-38.png)

또한 cURL은 해당 동작을 알리기 위해 경고를 출력합니다:

```
Warning: No remote file name, uses "curl_response"
```

다운로드한 파일에 사용자 지정 이름을 지정하려면, [`-o`](https://curl.se/docs/manpage.html#-o) (또는 `--output`) 플래그를 사용하십시오:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg"
```

cURL은 지정된 파일 URL로 GET リクエスト를 수행하고 다운로드된 콘텐츠를 `-o` 뒤에 지정된 이름으로 저장합니다. 이번에는 출력 파일이 `logo.jpg` 파일이 됩니다:

![The downloaded logo.jpg file in the folder where cURL was launched](https://github.com/bright-kr/curl-download-files/blob/main/image-39.png)

### Follow Redirects

일부 URL은 원하는 파일을 직접 가리키지 않으며, 최종 목적지에 도달하기 위해 자동 리다이렉트가 필요합니다.

cURL이 리다이렉트를 따라가도록 지시하려면 [`-L`](https://curl.se/docs/manpage.html#-L) 옵션을 사용하십시오:

```powershell
curl -O -L "<file_url>"
```

그렇지 않으면 cURL은 리다이렉션 レスポンス ヘッダー를 출력하고 `Location` ヘッダー에 제공된 새 위치를 따라가지 않습니다.

### Authenticate to the Server

일부 서버는 접근을 제한하고 사용자 認証을 요구합니다. 기본 HTTP 또는 FTP 認証을 수행하려면 [`-u`](https://curl.se/docs/manpage.html#-u) (또는 `--user`) 옵션을 사용하고 다음 형식으로 사용자 이름과 비밀번호를 지정하십시오:

```powershell
<username>:<password>
```

이 형식에서는 사용자 이름에 콜론을 포함할 수 없습니다. 그러나 비밀번호에는 콜론을 포함할 수 있습니다.

`<password>` 문자열은 선택 사항입니다. 사용자 이름만 지정하면 cURL이 비밀번호 입력을 요청합니다.

서버 認証을 사용하여 cURL로 파일을 다운로드하는 문법은 다음과 같습니다:

```powershell
curl -O -u <username>:<password> <file_url>
```

예를 들어, 다음 명령을 사용하면 認証이 필요한 URL에서 파일을 다운로드할 수 있습니다:

```powershell
curl -O -u "myUser:myPassword" "https://example.com/secret.txt"
```

### Impose Bandwidth Restrictions

사용 가능한 전체 帯域幅을 사용하지 않으려면, [`--limit-rate`](https://curl.se/docs/manpage.html#--limit-rate) 옵션 뒤에 설정하려는 최대 다운로드 속도를 지정하십시오:

```powershell
curl -O --limit-rate 5k "https://i.imgur.com/CSRiAeN.jpg"
```
출력은 다음과 비슷합니다:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   5166      0  0:00:06  0:00:06 --:--:--  5198
```

`--limit-rate` 옵션은 네트워크 과부하를 방지하고, 帯域幅 제한을 준수하거나, 테스트 목적의 느린 네트워크 조건을 시뮬레이션하기 위해 帯域幅 사용량을 제어하는 데 도움이 됩니다.

### Download Through a Proxy Server

프라이버시를 유지하거나 [レート制限 같은 アンチボット 조치](https://brightdata.co.kr/blog/web-data/anti-scraping-techniques)를 피하려면, IPアドレス를 숨기고 [`-x`](https://curl.se/docs/manpage.html#-x) (또는 `--proxy`) 옵션을 사용해 プロキシ를 통해 リクエスト를 라우팅하십시오:

```powershell
curl -x <proxy_url> -O <file_url>
```

`<proxy_url>`은 다음 형식으로 지정해야 합니다:

```powershell
[protocol://]host[:port]
```

프로キ시 URL은 HTTP, HTTPS 또는 SOCKS プロキシ를 사용하는지에 따라 달라집니다. 더 자세한 지침은 [cURL プロキ시 통합 가이드](https://brightdata.co.kr/blog/proxy-101/curl-with-proxies)를 참조하십시오.

예를 들어 HTTP プロキ시를 사용한다면, 명령은 다음과 같습니다:

```powershell
curl -x "http://proxy.example.com:8080" -O "https://i.imgur.com/CSRiAeN.jpg"
```

### Perform Background Downloads

출력에서 진행률 표시줄과 오류 메시지를 비활성화하려면, [`-s`](https://curl.se/docs/manpage.html#-s) (또는 `--silent`) 옵션을 사용하여 “quiet” 모드를 활성화하십시오:

```powershell
curl -O -s "https://i.imgur.com/CSRiAeN.jpg"
```

다운로드가 성공하면 터미널에 피드백 없이 현재 디렉터리에 파일이 나타납니다.

### Print Verbose Detail Information

오류가 발생했거나 cURL이 내부적으로 무엇을 수행하는지 더 잘 이해하려면, [`-v`](https://curl.se/docs/manpage.html#-v) (또는 `--verbose`) 옵션을 추가하여 verbose 모드를 사용하십시오:

```powershell
curl -O -v "https://i.imgur.com/CSRiAeN.jpg"
```

그러면 자세한 정보가 포함된 추가 출력이 활성화됩니다:

```
* IPv6: (none)
* IPv4: 146.75.52.193
*   Trying 146.75.52.193:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to i.imgur.com (146.75.52.193) port 443
* using HTTP/1.x
> GET /CSRiAeN.jpg HTTP/1.1
> Host: i.imgur.com
> User-Agent: curl/8.10.1
> Accept: */*
>
* Request completely sent off
* schannel: failed to decrypt data, need more data
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Length: 35354
< Content-Type: image/jpeg
< Last-Modified: Wed, 08 Jan 2025 08:02:49 GMT
< ETag: "117b93e0521ba1313429bad28b3befc8"
< x-amz-server-side-encryption: AES256
< X-Amz-Cf-Pop: IAD89-P1
< X-Amz-Cf-Id: wTQ20stgw0Ffl1BRmhRhFqpCXY_2hnBLbPXn9D8LgPwdjL96xarRVQ==
< cache-control: public, max-age=31536000
< Accept-Ranges: bytes
< Age: 2903
< Date: Wed, 08 Jan 2025 08:51:12 GMT
< X-Served-By: cache-iad-kiad7000028-IAD, cache-lin1730072-LIN
< X-Cache: Miss from cloudfront, HIT, HIT
< X-Cache-Hits: 1, 0
< X-Timer: S1736326272.410959,VS0,VE1
< Strict-Transport-Security: max-age=300
< Access-Control-Allow-Methods: GET, OPTIONS
< Access-Control-Allow-Origin: *
< Server: cat factory 1.0
< X-Content-Type-Options: nosniff
<
{ [1371 bytes data]
100 35354  100 35354    0     0   212k      0 --:--:-- --:--:-- --:--:--  214k
* Connection #0 to host i.imgur.com left intact.
```

### Set a Simplified Progress Bar

`-#` (또는 `--progress-bar`) 옵션을 사용하면 더 간단한 진행률 표시줄을 활성화할 수 있습니다:

```powershell
curl -O -# "https://i.imgur.com/CSRiAeN.jpg"
```

그러면 파일이 다운로드되는 동안 `#` 문자로 진행률 표시줄이 표시되며, 점진적으로 채워집니다:

```
########################################################### 100.0%
```

## How to Download Multiple Files with cURL

### Range File Download

중괄호 `{}`를 사용해 동일한 원격 URL의 여러 파일을 지정하여 다운로드할 수 있습니다:

```powershell
curl -O "https://example.com/images/{1.jpg,2.jpg,3.jpg}"
```

그러면 지정한 세 파일이 다운로드됩니다:

```
1.jpg
2.jpg
3.jpg
```

이 특정 경우에는 일반적인 정규표현식 `[]` 문법을 사용할 수도 있습니다:

```powershell
curl -O "https://example.com/files/file[1-3].jpg"
```

> **참고:**\
> 모든 사용자 지정 옵션(예: silent 모드의 `-s` 또는 帯域幅 제한의 `--limit-rate`)은 다운로드되는 모든 파일에 적용됩니다.

### Multiple File Download

서로 다른 URL에서 파일을 다운로드하려면 `-O` 옵션을 여러 번 지정해야 합니다:

```powershell
curl -O "https://i.imgur.com/CSRiAeN.jpg" -O "https://brightdata.co.kr/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg"
```

출력에는 지정된 각 URL별로 다운로드 바가 포함됩니다:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 35354  100 35354    0     0   271k      0 --:--:-- --:--:-- --:--:--  276k
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 22467    0 22467    0     0  34657      0 --:--:-- --:--:-- --:--:-- 34724
```

마찬가지로 `-o` 옵션을 여러 번 사용하여 파일의 사용자 지정 이름을 정의할 수 있습니다:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg" "https://brightdata.co.kr/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg" -o "blog_post.svg"
```

또한 `-O` 및 `-o` 옵션을 혼합해서 사용할 수도 있습니다:

```powershell
curl "https://i.imgur.com/CSRiAeN.jpg" -o "logo.jpg" -O "https://brightdata.co.kr/wp-content/uploads/2020/12/upload_blog_20201220_153903.svg"
```

> **참고:**\
> `-v`, `-s` 또는 `--limit-rate` 같은 다른 옵션은 모든 URL에 적용되므로 한 번만 지정해야 합니다.

## Best Practices When Downloading Files with cURL

아래는 cURL 파일 다운로드와 관련된 가장 중요한 모범 사례 목록입니다:

- Windows에서는 `Invoke-WebRequest` cmdlet과의 충돌을 피하기 위해 **curl 대신 `curl.exe`를 사용하십시오**.
- `-k` (또는 `--insecure`) 옵션으로 **HTTPS 및 SSL/TLS 오류를 무시하십시오**.
- **올바른 HTTP 메서드를 지정하십시오**: `-X` 옵션을 사용해 GET, POST 또는 PUT을 지정합니다.
- 공백, 앰퍼샌드 등으로 인한 문제를 피하기 위해 **URL을 따옴표로 감싸고 특수 문자는 `\`로 이스케이프하십시오**.
- **신원을 보호하기 위해 プロキ시를 지정하십시오**: `-x` (또는 `--proxy`) 옵션을 사용합니다 .
- 서로 다른 リクエスト 간에 **Cookie를 저장하고 재사용하십시오**: 각각 `-c` 및 `-b` 옵션을 사용합니다.
- **더 나은 제어를 위해 다운로드 속도를 제한하십시오**: `--limit-rate` 옵션을 사용합니다.
- **디버깅을 위해 verbose 출력을 추가하십시오**: `-v` 옵션을 사용합니다.
- **오류 レスポンス를 확인하십시오**: `-w` 옵션을 사용해 HTTP レスポンス 코드를 항상 확인하십시오.

## cURL vs Wget for Downloading Files

- **cURL**은 데이터 전송에 대해 더 세밀한 제어가 가능하며, 사용자 지정 ヘッダー, 認証, 더 많은 프로토콜을 지원합니다.
- **Wget**은 더 단순하며, 대량 다운로드, 재귀 처리, 중단된 전송 처리에 더 적합합니다.

## Conclusion

HTTP リクエ스트를 할 때마다 인터넷에 흔적이 남습니다. 신원과 프라이버시를 보호하고 보안을 강화하기 위해, cURL에 プロキ시를 통합하는 것을 고려해 보십시오:

- [Datacenter proxies](https://brightdata.co.kr/proxy-types/datacenter-proxies) – 770,000개 이상의 データセンタープロキ시 IP.
- [Residential proxies](https://brightdata.co.kr/proxy-types/residential-proxies) – 195개 이상의 국가에서 7,200만 개 이상의 レジデンシャルプロキシ IP.
- [ISP proxies](https://brightdata.co.kr/proxy-types/isp-proxies) – 700,000개 이상의 ISPプロキシ IP.
- [Mobile proxies](https://brightdata.co.kr/proxy-types/mobile-proxies) – 700만 개 이상의 モバイルプロキシ IP.

[Sign up now](https://brightdata.co.kr) and test our proxies and scraping solutions for free!