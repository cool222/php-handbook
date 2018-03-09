# 微信开发
## （一）概述
## （二）常用函数
### 1.微信公众号授权

```php
/**
* getWeixinUser
* 获得微信公众号授权
* 注意：适应于 CI 框架，非 CI 框架请修改 $this->session 和 require_once "wxjs.php" 的位置
*
* 依赖关系：
* wexin/wxjs.php
* 
* 参数：
* $appid    :公众号AppID
* $secrit   :公众号AppSecrit
* $scope    :"base"或"userinfo"，
*
* 返回值：
* $scope为"base"时,返回openid（静默授权）
* $scope为"userinfo"时,返回完整用户信息（可能会有显式授权）
*
* 使用方法：
* 在控制器基类构造函数中使用 $user=getWeixinUser($appid,$secrit,"userinfo") 即可
*/
function getWeixinUser($appid,$secrit,$scope="base")
    if(empty($_GET['code'])){
        $url = $_SERVER["REQUEST_SCHEME"] . "://" . $_SERVER["SERVER_NAME"] . $_SERVER["REQUEST_URI"]; //这是要回调地址可以有别的写法
        redirect("https://open.weixin.qq.com/connect/oauth2/authorize?appid={$appid}&redirect_uri={$url}&response_type=code&scope=snsapi_" . $scope . "&state=STATE#wechat_redirect");
        //重定向到以上网址,这是微信给的固定地址.必须格式一致
    }else{
        //回调成功,获取code,再做请求,获取openid

        $j_access_token=file_get_contents("https://api.weixin.qq.com/sns/oauth2/access_token?appid={$appid}&secret={$secret}&code={$_GET['code']}&grant_type=authorization_code");
        $a_access_token=json_decode($j_access_token,true);
        $access_token=$a_access_token["access_token"];//虽然这里 也获取了一个access_token,但是和获取用户详情,还有发送模板信息所使用的access_token不同
        $openid=$a_access_token["openid"];
        $this->session->set_userdata("wx_openid", $openid);                
        
        if ($scope=="userinfo")
        {
            require_once BASEPATH.'/third_party/weixin/wxjs.php';
            $wxjs = new JSSDK( $appid, $secrit);
            
            //获得用户数据
            $url="https://api.weixin.qq.com/cgi-bin/user/info?access_token=" .$wxjs->getAccessToken() ."&openid=" . $openid . "&lang=zh_CN";
            $j_userinfo=file_get_contents($url);

            $userinfo=json_decode($j_userinfo,true);
            if (!$userinfo['nickname'])
            {
                //如果没有用户昵称，则用另外的方式获得用户数据
                $j_userinfo=file_get_contents("https://api.weixin.qq.com/sns/userinfo?access_token={$access_token}&openid={$openid}&lang=zh_CN");
                $userinfo=array_merge($userinfo,json_decode($j_userinfo,true));
            }
            return $userinfo;
        }
        else
        {
            return $openid;
        }
        
    }

```