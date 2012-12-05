Upload-Class-for-myqee-v2
=========================
本类来自于Codeigniter，本着不重复轮子基准。当然ci上传类对于mimes的验证还是很不错的。
####获取一个实例化对像

    $upload = new Upload();
    OR:
    $upload = Upload::factory();

####偏好设置
根据你的偏好设置来控制要被上传的文件，在控制器里，建立了如下的偏好设置：

    $config['upload_path']   = './uploads/';
    $config['allowed_types'] = 'gif|jpg|png';
	$config['max_size']      = '100';
	$config['max_width']     = '1024';
	$config['max_height']    = '768';

	$upload = new Upload($config);
    OR:
    $upload = Upload::factory($config);
	
	
当然你也可以：
	
	$upload = new Upload();
	$upload->set_upload_path    = './uploads/';
	$upload->set_allowed_types  = 'gif|jpg|png';
	$upload->set_max_size       = '100';
	$upload->set_max_width      = '1024';
	$upload->set_max_height     = '768';
	…
	…
	
	
#####偏好设置参数
偏好设置 | 默认值 | 选项 | 描述
:----------- | :------------ | :----------- | :-----------
mimes_check|false|true/false (boolean)|严谨验证文件mimes，默认仅对文件后缀做验证。如果开启则判断文件mimes与后缀是否一致，且是否在允许的上传范围内。（如果使用flash上传控件如swfuplpad，这类控件会将文件mimes擦写成application/octet-stream等类似字串，这种应用下是不能开启本验证的，否则文件验证会全部不通过。当然了在这种情况下如果对控制要求高，可以扩展Upload类做文件头部编码验证。）另外：开启mimes验证后，需要确定你定义的上传文件类型是否在 /libraries/myqee/core/config/mimes.config.php 有定义，并且不同线上环境对文件mimes定义也稍有不同。我们会持续的丰富mimes范围。
upload_path|none|none|文件上传路径。该路径必须是可写的，相对路径和绝对路径均可以。
allowed_types|none|none|允许上传文件的MIME类型；通常文件扩展名可以做为MIME类型. 允许多个类型用竖线‘|’分开
file_name|none||想要使用的文件名如果设置了这个参数，将根据这里设置的文件名来对上传的文件进行重命名。
overwrite|false|true/false (boolean)|是否覆盖。该参数为TRUE时，如果上传文件时碰到重名文件，将会把原文件覆盖；如果该参数为false，将会在新文件的文件名后面加一个数字。
max_size|0|	none|允许上传文件大小的最大值（以K为单位）。该参数为0则不限制。注意：通常PHP也有这项限制，可以在php.ini文件中指定。通常默认为2MB。
max_width|0|none|上传文件的宽度最大值（像素为单位）。0为不限制。
max_height|	0|none|上传文件的高度最大值（像素为单位）。0为不限制。
max_filename|0|none|文件名的最大长度。0为不限制。
encrypt_name|false|	true/false (boolean)|是否重命名文件。如果该参数为true，上传的文件将被重命名为随机的加密字符串。当你想让文件上传者也不能区分自己上传的文件的文件名时，是非常有用的。当 overwrite 为 false 时，此选项才起作用。
remove_spaces|true|true/false (boolean)|	参数为true时，文件名中的空格将被替换为下划线。推荐使用。

####do_upload()
根据你的偏好配置参数执行操作。注意：默认情况下上传的文件来自于提交表单里名为userfile的文件域,并且该表单必须是 "multipart"类型：
	
	<form method="post" action="some_action" enctype="multipart/form-data" />
	
如果你想在执行do_upload函数之前自定义自己的文件域名称，可通过以下方法实现：
	
	$upload      = upload::factory();
	$field_name  = "some_field_name";
	$upload->do_upload($field_name);
	
####display_errors()
如果do_upload()返回失败，显示错误信息。此函数不会自动输出，而是返回数据，所以你可以按你的要求安排。

######格式化错误
上面的函数默认使用<p>标记错误信息。你可以像这样设置自己的分隔符。
	
	$upload->display_errors('<div>', '</div>');

####data()
这是一个辅助函数，它返回你上传文件的所有相关信息的数组。
	
	$result = $upload->data();
	print_r($result);
	Array
	(
    	[file_name]    => mypic.jpg
    	[file_type]    => image/jpeg
    	[file_path]    => /path/to/your/upload/
    	[full_path]    => /path/to/your/upload/jpg.jpg
    	[raw_name]     => mypic
    	[orig_name]    => mypic.jpg
    	[client_name]  => mypic.jpg
    	[file_ext]     => .jpg
    	[file_size]    => 22.2
    	[is_image]     => 1
    	[image_width]  => 800
    	[image_height] => 600
    	[image_type]   => jpeg
    	[image_size_str] => width="800" height="200"
	)

######解释

Item|Description
:----------- | :------------ 
file_name|已上传的文件名（包括扩展名）
file_type|文件的Mime类型
file_path|不包括文件名的文件绝对路径
full_path|包括文件名在内的文件绝对路径
raw_name|不包括扩展名在内的文件名部分
orig_name|上传的文件最初的文件名。这只有在设置上传文件重命名（encrypt_name）时才有效。
client_name|上传的文件在客户端的文件名。
file_ext|文件扩展名（包括‘.’）
file_size|图像大小，单位是kb
is_image|是否是图像。 1 =是图像。 0 = 不是图像。
image_width|图像宽度.
image_height|图像高度
image_type|文件类型，即文件扩展名（不包括‘.’）
image_size_str|一个包含width和height的字符串。用于放在一个img标签里。
	
###说明
本类仅负责上传处理，对于上传目录的建立与权限定义并未包含。我认为这也是正确的做法。如特殊应用需求，可自行将Upload类扩展 如对set_upload_path方法或者 validate_upload_path方法进行扩展


###例子
某个简单的应用例子(结合KindEditor富文本编辑器)
		
		
		$_error   = 1;
		$_msg     = '上传不成功';
		$_url     = '';
		$_id      = '';
        $path     = 'upload/'.date("Ym").'/';
        
        //create dir 处理
       	…
       	…
       	…
       	
    	$config  = array(
            'allowed_types' => 'jpg|jpeg|png|gif',
            'upload_path'   => DIR_WWWROOT.$path,
            'max_size'      => '2000',
            'encrypt_name'  => true,
        );

        $upload = Upload::factory($config);

        if($upload->do_upload('imgFile'))
        {
            $upload_data    = $upload->data();
            $orm_upload     = new ORM_Upload_Finder();
            $create         = $orm_upload->create();
            $create->type   = 'product';
            $create->path   = $path.$upload_data['file_name'];
            $create->insert();

            if($create->id)
            {
            	$_error = 0;
            	$_msg   = '';
            	$_url   = '/'.$path.$upload_data['file_name'];
            	$_id    = $create->id;
            }
            else
            {
                @unlink($upload_data['full_path']);
            }
        }
        else
        {
            $msg = $upload->display_errors('','');
        }
        header('Content-type: text/html; charset=UTF-8');
        echo json_encode( array('error' => $_error,'msg' => $_msg,'url' => $_url,'id' => $_id) );
        exit;
	