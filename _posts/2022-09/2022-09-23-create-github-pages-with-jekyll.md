---
layout: post
title: Create Github Pages with Jekyll
date: 2022-09-23 20:44 +0800
description:
image:
category: [Github]
tags: [ruby, jekyll, github]
author: cloverwhale
---
紀錄使用Jekyll建立Github網站的過程, 原本這一個網站是自己寫的陽春html... 而且是一頁式 XD

## 參考的資料
- [Jekyll官方文件](https://jekyllrb.com/docs/installation/)
- [Jekyll Theme Chirpy Demo](https://chirpy.cotes.page/)
- [無痛轉移Medium到自架網站](https://zhgchg.li/posts/a0c08d579ab1/)
- [How to Override CSS Style in Jeykll](https://tomkadwill.com/2017/12/16/how-to-override-css-styles-in-jekyll.html)

# Steps
## 安裝Ruby

Mac本身有default的版本, 但Jekyll建議使用chruby安裝另一個版本的Ruby, 沒有Homebrew的話先安裝[Homebrew](https://brew.sh/)

Homebrew安裝chruby並安裝(更新)Ruby
```zsh
brew install chruby ruby-install
ruby-install ruby
```
設定Shell預設使用chruby (使用Bash則要更新.bash_profile)
```zsh
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.2" >> ~/.zshrc # run 'chruby' to see actual version
```
檢查ruby版本是否為最新版本
```zsh
ruby -v
```

## 安裝Jekyll和Bundler
```zsh
gem install jekyll bundler
```

## 使用Chirpy

使用[Chirpy Starter](https://github.com/cotes2020/chirpy-starter/generate)建立新的repo, 並將repo名稱改成cloverwhale.github.io

clone remote repo: 我直接在VSCode中操作 click Source Control > Clone Repository > Clone from Github (需要授權)

在project root direcotry底下, 執行
```zsh
bundle
```

執行
```zsh
bundle lock — add-platform x86_64-linux
```
這一行會在Gemfile.lock的PLATFORM底下, 加上x86_64-linux. 我不是很清楚為什麼要這麼做, 但是沒有這一行會無法在Github上deploy(我試著刪除這一行後push, Actions底下會看到錯誤)



## 修改 _config.yml
照著註解修改成自己要的資料, 我遇到一個問題是baseurl的設定.

看著解釋以為要填上'/cloverwhale.github.io', 但deploy到Github之後發現css的位置有問題(local是好的). 後來改成空白才有正確顯示
```yaml
# Change the following value to '/PROJECT_NAME' ONLY IF your site type is GitHub Pages Project sites
# and doesn't have a custom domain.
baseurl: ''
```

## 預覽

```zsh
bundle exec jekyll s
```
打開 http://127.0.0.1:4000/ 可顯示目前網站的樣子


## 新增文章

- 在_post目錄下建立YYYY-MM-DD-TITLE.md的檔案 (檔案名稱要正確, 不然會無法顯示)
- 編輯md檔案, 開頭要include [Front Matter](https://jekyllrb.com/docs/front-matter/)
```yaml
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-TTTT # +/-TTTT是timezone, 例如+0800
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG]     # TAG names should always be lowercase
---
```

## Jekyll-Compose
但是每一次要自己填有點麻煩, 所以另外安裝[Jekyll-Compose](https://github.com/jekyll/jekyll-compose)

把下面這一行加到Gemfile中
```
gem 'jekyll-compose', group: [:jekyll_plugins]
```
然後執行
```zsh
bundle
```

之後就可以使用下面語法新增post(其他語法就看Jekyll-Compose說明)
```zsh
bundle exec jekyll post "New Post Title"
```

然後要修改Front Matter話, 在_config.yml加上:
```yaml
jekyll_compose:
  default_front_matter:
    posts:
      description:
      image:
      category:
      tags:
      author: cloverwhale
```
(因為我還沒想到我的usecase 所以先這樣...)

還有一個自動開啟editor的功能, 等我弄懂再補上. (目前就是在VSCode中編輯對應的檔案)


## 把local的東西丟到Github上

就是git add, git commit, git push這樣.

> 要注意的是需要在Repository > Settings底下把deploy的branch改成gh-pages 
{: .prompt-tip }

然後看Actions裏面是否完成deployment即可.

## 不是很重要但我就愛改顏色

在assets底下新增css/style.scss

編輯這個檔案, 先加上下面的語法, 在這之後就可以寫自己要的樣式了 (如果是其他的theme就要改成對應的名稱)
```scss
---
---

@import "jekyll-theme-chirpy";
```

## 結語

以上就是一個完全看不懂Ruby在幹嘛的人, 一直做Google search的步驟...

其他如何新增favicon等等就是[Chirpy Demo](https://chirpy.cotes.page/)網站都找的到.

About頁面需要編輯_tabs底下的about.md

檔案位置要放對.
