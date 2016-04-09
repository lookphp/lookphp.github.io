#PHP 多文件上传实例<small>以图片上传为例</small>

Part 1 : html篇

```
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	<form action="upload.php" method="post" enctype="multipart/form-data">
		<input type="file" name="img[]" multiple>
		<input type="submit">
	</form>
</body>
</html>
```

Part 2 : PHP篇
```
<?php
echo '<pre>';
//接收前端请求的数据
$img = $_FILES['img'];

//将图片上传到服务器中（可以在这里对图片进行处理，具体的另写吧）
if(!empty($img))
{
    $img_desc = reArrayFiles($img);
    print_r($img_desc);
    
    foreach($img_desc as $val)
    {
        $newname = date('YmdHis',time()).mt_rand().'.jpg';
        move_uploaded_file($val['tmp_name'],'./uploads/'.$newname);
    }
}

//修改上传的数组格式
function reArrayFiles($file)
{
    $file_ary = array();
    $file_count = count($file['name']);
    $file_key = array_keys($file);
    
    for($i=0;$i<$file_count;$i++)
    {
        foreach($file_key as $val)
        {
            $file_ary[$i][$val] = $file[$val][$i];
        }
    }
    return $file_ary;
}

```