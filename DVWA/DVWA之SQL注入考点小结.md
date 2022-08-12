### SQL Injection

SQL Injection，即[SQL注入](https://so.csdn.net/so/search?q=SQL%E6%B3%A8%E5%85%A5&spm=1001.2101.3001.7020)，是指攻击者通过注入恶意的SQL命令，破坏SQL查询语句的结构，从而达到执行恶意SQL语句的目的。SQL注入漏洞的危害是巨大的，常常会导致整个数据库被“脱裤”，尽管如此，SQL注入仍是现在最常见的Web漏洞之一。近期很火的大使馆接连被黑事件，据说黑客依靠的就是常见的SQL注入漏洞。

**low：**

这个等级主要考的是字符型注入。最基本的注入，通过盲注，union注入都可以。

```
$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
```


**Medium：**

这个等级主要考的是post提交方式的数字型注入，而且使用了mysql\_real\_escape\_string()函数进行转义(但是并没有什么卵用，因为是数字型注入)，虽然前段页面控制了提交的参数，但是我们可以通过抓包改包进行注入！ 通过后台抓包修改数据包，然后盲注、union注入都可以

```
 $id = $_POST[ 'id' ];       
 $id = mysqli_real_escape_string($GLOBALS["___mysqli_ston"], $id);        
 $query  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";
```


**High:**

这个等级有一个特点就是查询提交页面与查询结果页面显示的不是同一个，也没有执行302重定向，这样做的目的是为了防止一般的软件注入，因为软件在注入过程中，无法在查询提交页面上获取查询的结果，没有了反馈，也就没办法进一步注入，所以这题得手工注入。

这个等级是字符型的注入，而且使用了 limit 1 用来限制显示的列数。

我们可以使用 1' and 1=2 # 来进行注入，盲注、union注入都可以。

```
 $id = $_SESSION[ 'id' ];        
 $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";
```


**Impossible：**

可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，同时只有返回的查询结果数量为一时，才会成功输出，这样就有效预防了“脱裤”，Anti-CSRFtoken机制的加入了进一步提高了安全性。

```
<?php       
if( isset( $_GET[ 'Submit' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );       
    // Get input       
    $id = $_GET[ 'id' ];       
    // Was a number entered?       
    if(is_numeric( $id )) {       
        // Check the database       1
        $data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );       1
        $data->bindParam( ':id', $id, PDO::PARAM_INT );       1
        $data->execute();       1
        $row = $data->fetch();       1
        // Make sure only 1 result is returned       1
        if( $data->rowCount() == 1 ) {       1
            // Get values       1
            $first = $row[ 'first_name' ];       1
            $last  = $row[ 'last_name' ];       1
            // Feedback for end user       2
            echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";       2
        }       2
    }       2
}       2
// Generate Anti-CSRF token       2
generateSessionToken();       2
?>
```


### Blind SQL Injection

**low:**

这个主要考的是boolean盲注。当你的查询语句能查到数据时，页面固定显示 User ID exists in the database ，当查询不到数据或者语句语法错误时，固定显示 User ID is MISSING from the database. 。所以，这题只能利用boolean盲注或者 sleep延时注入来进行注入。

**Medium:**

这个是考的post方式的boolean盲注，注入方面和上面一样，只是需要抓包进行修改注入。