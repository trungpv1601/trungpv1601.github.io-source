---
title: Cấu hình tự động deploy PHP
date: 2019-03-23 12:07:48
tags:
---

> Trước nay có làm rất nhiều projects về PHP nhưng làm một mình và khá là lười setup linh tinh, nên mình thường chơi kiểu nông dân push code lên git và update code trên server bằng cách pull về.

> Nay lười quá vì phải update code trên nhiều con server và làm nhiều projects hơn. Do đó, việc update code mất nhiều thời gian do đó muốn viết 1 bài về tự động hoá việc này. Một phần note lại kiến thức đã tìm hiểu. Vì trước giờ không có thói quen viết blog hoặc note chi tiết các bước mình đã làm nên cảm thấy phí thời gian mình tìm hiểu. Trong khi, một thời gian sau làm lại thì lại phải tìm lại những thứ đã làm trước đó vì quên ;). Cũng như để lại gì đó trong profile của mình. Sorry hơi dài dòng...

## 0. Tạo user trên Remote Server

```
sudo adduser deployer
sudo usermod -aG www-data deployer
sudo chfn -o umask=022 deployer
sudo chown deployer:www-data /var/www/html
sudo chmod g+s /var/www/html
```
Sau đó login vào user deployer và tạo ssh key 

```
su - deployer
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```
Sau khi chạy lệnh trên xong copy key đó add vô github hoặc bitbucket.

Tiếp tục là tạo ssh key kết nối giữa local và Remote server thông qua user deployer
```
ssh-keygen -t rsa -b 4096 -f  ~/.ssh/deployerkey
cat ~/.ssh/deployerkey.pub
```

Copy ssh key trên add vào Remote server với user deployer
```
nano ~/.ssh/authorized_keys
```
Paste vào và Ctr + X, nhấn Y và ENTER lưu lại

```
chmod 600 ~/.ssh/authorized_keys
```

Done

Giờ kết nối vô Remote server với user deployer bằng cách
```
ssh deployer@your_server_ip  -i ~/.ssh/deployerkey
```

Lệnh này kiểm tra xem kết nới với git chưa
```
ssh -T git@mygitserver.com
```

## 1. Cài PHP Deployer ([Read More](https://deployer.org/))

```
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep
```

## 2. Khởi tạo Deployer trong project

```
dep init
```

Sau khi tạo thì nó sẹ tạo ra file deploy.php trong project. Chúng ta sẽ cấu hình trong đó.

## 3. Cấu hình server cho deploy.php

Ta có thể cấu hình nhiều môi trường deploy: production, staging hay dev...

```
set('branch', 'master');
host('x.x.x.x')
    ->stage('production')
    // ...
    ;
host('x.x.x.x')
    ->stage('staging')
    // ...
    ->set('branch', 'develop');
```

## 4. Cấu hình tasks để chạy khi deploy

```
task('create-new-release', function () {
    // ...
});
task('create-symlinks', function () {
    // ...
});
task('compile-assets', function () {
    // ...
});
task('deploy', [
    'create-new-release',
    'create-symlinks',
    'compile-assets',
]);
```

## 5. Một số lệnh deploy

- Chạy từng task trên tất cả các hosts
```
dep compile-assets
```

- Chạy từng task trên một một host
```
dep compile-assets production
```

## 6. Sample deploy.php

[https://gist.github.com/trungpv1601/eedde3ca6f04f2129945bbf1fec42929](https://gist.github.com/trungpv1601/eedde3ca6f04f2129945bbf1fec42929)

## Credit
- [https://www.codepicky.com/php-automatic-deploy/](https://www.codepicky.com/php-automatic-deploy/)

