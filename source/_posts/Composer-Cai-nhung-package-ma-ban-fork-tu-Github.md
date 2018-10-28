---
title: 'Composer: Cài những package mà bạn fork từ Github'
date: 2018-10-28 10:24:32
tags: 
- TIL
- Composer
- Github
---

## Composer: Cài những package mà bạn fork từ Github

- Ví dụ tôi fork một package từ https://github.com/backup-manager/backup-manager
- Package của tôi https://github.com/trungpv1601/backup-manager trong này tôi có thêm một số hàm cần thiết cho mình.
- Thêm đoạn bên dưới vào composer.json dùng version "dev-master"

```
{
    "repositories":
    [
            {
                "type": "vcs",
                "url": "https://github.com/trungpv1601/backup-manager"
            }
    ],
    "require":
    {
        "backup-manager/backup-manager": "dev-master"
    }
}
````