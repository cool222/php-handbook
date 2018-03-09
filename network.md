# 网络访问
## 一、HTTP
### 1.POST
```php
/**
* http_request
* 以POST方式提交数据
*
* 作用：
* 向第三方服务器提交，获得返回的数据，如向微信服务器获取用户授权信息
*
* 参数列表：
* $url:网址URL，字符串
* $data:POST提交的数据，可以为数组或字符串，提交字符串时 $raw 为true
* $raw:是否以RAW格式提交数据
*
* 返回值：
* 取得的结果字符串
*
*/
function http_request($url,$data=array(),$raw=false){
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
    if ($raw)
    {
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'X-AjaxPro-Method:ShowList',
            'User-Agent:Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/33.0.1750.154 Safari/537.36',
            'Content-Type: text/plain',
         ));
    }
    // POST数据
    curl_setopt($ch, CURLOPT_POST, 1);
    // 把post的变量加上
    curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($data));
    $output = curl_exec($ch);
    curl_close($ch);
    return $output;
}
 ```
 