---
title: 图片裁剪(cropper)后上传问题
date: 2018-04-12 16:24:31
tags: JavaScript
categories: 前端
---

[《有道笔记-图片裁剪(cropper)后上传问题》](http://note.youdao.com/noteshare?id=11702ca3a55611eb4658ae26087d97a3)

## 一、图片裁剪(cropper)后上传问题

Cropper 是一个很好用的图片裁剪插件，具体效果可以看下官网。[《Cropper 官网》](http://fengyuanchen.github.io/cropper/)

![](https://ws3.sinaimg.cn/large/006tNc79gy1fq9yj3669hj30um0wotaa.jpg)

如何使用 React 的话，可以使用`react-cropper`。

但是存在一个问题，那就是 Cropper 裁剪后，生成的是一个 canvas 对象，然后可以导出 base64 的数据格式。

**但是项目要求把图片上传，返回一个图片地址，那么如何把 base64 变成一个图片上传呢 **

## 二、Ajax 上传 base64 的图片

下面这段代码就可以实现 base64 的图片上传，参照[《图片裁剪(cropper)后上传问题》](https://www.cnblogs.com/bbbiu/p/6760505.html)

> 这边参考文章中，使用 `formData.append('uploadFile', bolbImg, nameImg)` 我这边就上传失败，这个应该跟后端上传接口的实现方式有关。

```javascript
// 生成一个bolb文件
let bolbImg = convertBase64UrlToBlob(this.state.imgData)

// 构建一个formData对象
let formData = new FormData()

let nameImg = `${new Date().getTime()}.png`

// 添加到 file属性里面
formData.append('file', bolbImg, nameImg)

// post提交，就可以上传图片了
Axios.post('/php/webuploader.php', formData).then(resp => {
  this.props.onClose(resp.data)
})
```

上传图片的接口 php

```php
<?php
function resize_image($uploadedfile, $dst)
{
	// 	上传的图片文件;
	$src = imagecreatefromjpeg($uploadedfile);
	// 	原图片尺寸;
	list($width, $height) = getimagesize($uploadedfile);

	// 	新图片尺寸;
	if ($width > 1920) {
		$newwidth = 1920;
		$newheight = ($height / $width) * 1920;
	}
	else if ($height > 1080) {
		$newheight = 1080;
		$newwidth = ($width / $height) * 1080;
	}
	else {
		$newheight = $height;
		$newwidth = $width;
	}

	// 	按新尺寸创建临时图片文件;
	$tmp = imagecreatetruecolor($newwidth, $newheight);
	// 	压缩图片到临时文件;
	imagecopyresampled($tmp, $src, 0, 0, 0, 0, $newwidth, $newheight, $width, $height);
	// 	将临时文件保存到指定文件;
	$ret = imagejpeg($tmp, $dst, 100);

	imagedestroy($src);
	imagedestroy($tmp);

	return $ret;

}

// 设置上传目录;
$path = "../uploads/";
$returnPath = "/uploads/";

if (!empty($_FILES)) {
	// 	得到上传的临时文件流;
	$tempFile = $_FILES['file']['tmp_name'];
	// 	允许的文件后缀;
	$fileTypes = array('jpg', 'jpeg', 'gif', 'png', 'mp3', 'mp4', 'lrc', 'obj');
	// 	图片文件后缀;
	$imgFile = array('jpg', 'jpeg', 'png');
	// 	得到文件原名;
	// 	$	fileName = iconv("UTF-8","GB2312",$_FILES["file"]["name"]);
	$fileName = $_FILES["file"]["name"];
	// 	上传文件的后缀;
	$ext = pathinfo($fileName, PATHINFO_EXTENSION);
	$ext = strtolower($ext) || 'png';
	if ($fileName && !in_array($ext, $fileTypes)) {
		echo $fileName . "--file type not allowed!2";
		exit();
	}

	$rand = md5(time() . mt_rand(1, 10000));
	// 	随机文件名;
	$fileName = $rand . substr($fileName, -4);

	// 	保存的文件名;
	$fileParts = pathinfo($_FILES['file']['name']);

	// 	接受动态传值;
	// 	$files=$_POST['typeCode'];
	;
	header("Content-Type: text/html; charset=utf-8");
	// 	最后保存服务器地址;
	if (!is_dir($path)) {
		mkdir($path);
	}

	// 	文件上传;
	$ret = move_uploaded_file($tempFile, $path . $fileName);

	if (!$ret) {
		echo $fileName . "--file upload error!";
		exit();
	}
	// 	上传成功，返回保存的文件路径;
	if (in_array($ext, $imgFile)) {
		// 		图片文件，判断是否需要压缩 原图片尺寸;
		list($width, $height) = getimagesize($uploadedfile);
		if ($width > 1920 || $height > 1080) {
			// 			压缩图片;
			$ret = resize_image($tempFile, $path . $fileName);
			if (!$ret) {
				echo $fileName . "--image resize error!";
				exit();
			}
		}
	}
	echo json_encode($returnPath . $fileName);
}
?>
```
