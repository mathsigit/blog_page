---
layout:     post
title:      "如何在GitHub Pages使用Jekyll建置部落格"
subtitle:   "Using Jekyll as a static site generator with GitHub Pages."
date:       2017-11-07 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

<h1>在GitHub Pages使用Jekyll建置部落格</h1>
在上一篇開幕首篇內文中提到，[為何使用GitHub架設Blog後][newopen]，這次要來說明如何搭配Jekyll來建立Blog。
開發環境是使用macOS Sierra 10.12.6。

---
<h3 id="why_jekyll">為何是Jekyll?</h3>
由於GitHub Page只能放置靜態網站，GitHub Page也支援Jekyll，說明文件也非常豐富，所以就選他啦！完整的介紹請看這裡：[Using Jekyll as a static site generator with GitHub Pages][jekyll_on_githubpage]

在安裝Jekyll前，必須確認電腦是否已經安裝了下列的工具：
* [`Ruby`][ruby]
* [`Gem`][gem]

如果假設你的環境已經安裝Jekyll，可以跳過直接跳到[建置blog](#new_blog)。

---
<h3 id="install_ruby">安裝Ruby</h3>
由於Ruby在macOS已經內建就不用安裝了。    
Windows使用者請參考[`RubyInstall`][rubyinstall_wins]。    
linux/UNIX作業系統可以使用[`rbenv`][rbenv] 或 [`RVM`][RVM]，或是直接使用`yum install`等系統指令安裝。

安裝完畢後，執行下列指令確認是否安裝成功。    
```bash
ruby -v
```

如果出現類似下列的訊息表示已經安裝成功了:    
```bash
ruby 2.4.2p198 (2017-09-14 revision 59899) [x86_64-darwin16]
```
---
<h3 id="install_gem">安裝Gem</h3>
Gem是Ruby的一個套件安裝與管理的套件，Jekyll必須透過Gem來安裝與管理。
如果已經成功安裝Ruby，Gem也會同時被安裝。

確認Gem是否正常運作：    
```bash
gem -v
```    
如果正常運作，會顯示出版本號碼。以作者撰寫文章時所使用的Gem版本為 : **2.6.14**

現在來讓我們更新Gem吧！    
```bash
gem update --system
```
經過一連串的更新後，如果沒出現錯誤訊息就可以開始安裝Jekyll了。

---
<h3 id="install_jekyll">安裝Jekyll</h3>
要使用[`Jekyll`][jekyll]初始化一個blog的檔案架構，當然要先安裝Jekyll。

使用下列指令來安裝Jekyll:    
```bash
gem install jekyll
```    
安裝完畢後，使用下列指令測試是否安裝成功：    
```bash
jekyll -v
```    
如果成功會顯示Jekyll版本號碼，以作者撰寫文章時所使用的版本為 : **jekyll 3.6.2**

---
<h3 id="new_blog">建置你的部落格</h3>
如果進行到這個步驟，相信你已經安裝好Jekyll。    
首先我們要先使用jekyll建立並初始化一個新blog專案，並且進入blog專案資料夾內：    
```bash
jekyll new myblog #建立並初始化一個新blog專案
cd myblog         #進入blog專案資料夾
```
如果成功會出現下面的訊息：
```bash
Running bundle install in /Volumes/Sdhd/Documents/project/mathsigit/github/test...
  Bundler: The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
  Bundler: Fetching gem metadata from https://rubygems.org/...........
  Bundler: Fetching gem metadata from https://rubygems.org/.
  Bundler: Resolving dependencies...
  Bundler: Fetching public_suffix 3.0.1
  Bundler: Installing public_suffix 3.0.1
  Bundler: Using addressable 2.5.2
  Bundler: Using bundler 1.16.0
  ...
```
Blog專案資料夾結構如下：
```bash
-rw-r--r--  1 stana  staff   398B Nov 10 00:16 404.html
-rw-r--r--  1 stana  staff   937B Nov 10 00:16 Gemfile
-rw-r--r--  1 stana  staff   1.3K Nov 10 00:17 Gemfile.lock
-rw-r--r--  1 stana  staff   1.6K Nov 10 00:16 _config.yml
drwxr-xr-x  3 stana  staff   102B Nov 10 00:16 _posts
-rw-r--r--  1 stana  staff   539B Nov 10 00:16 about.md
-rw-r--r--  1 stana  staff   213B Nov 10 00:16 index.md
```
可以清楚看到基本上都是由Markdown組成，當然你也可以使用`.html`檔案格式來撰寫你的網頁！

接下來可以啟動jekyll server來瀏覽我們的blog啦！
```bash
jekyll serve
```
如果server啟動成功會出現下列訊息：
```bash
Configuration file: /Volumes/Sdhd/Documents/project/mathsigit/github/test/_config.yml
            Source: /Volumes/Sdhd/Documents/project/mathsigit/github/test
       Destination: /Volumes/Sdhd/Documents/project/mathsigit/github/test/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 0.52 seconds.
 Auto-regeneration: enabled for '/Volumes/Sdhd/Documents/project/mathsigit/github/test'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```
打開瀏覽器後在網址列輸入`http://127.0.0.1:4000/`看到你的部落格啦（撒花）
![Photo not found][jekyll_init_photo]

---
<h3 id="create_post">新增文章</h3>
Blog專案結構中可以看到一個叫做_posts的資料夾，Blog內所有的文章都會存放在裡面，檔案名稱都是以year-month-day-name來顯示，副檔名可以使用`.markdown` 或是 `.md`。

---
<h3 id="ui_for_blog">美化你的Blog</h3>
使用Jekyll所建置出來的網站雖然是靜態網站，但由於Jekyll有超多的Plugin可以使用，例如站內搜尋、建立文章tag等等功能..都有，跟動態網站可以達到的功能已經相差不遠。再加上Bootstrap加持下，個人專屬的部落格要多花俏就有多花俏。

那如果都不會寫JS、Bootstrap的話，該怎麼辦？！    
沒關係！這一點靠著社群的支援也可以快速讓Blog瞬間美感上升！   

在 [jekyll / jekyll][jekyll_themes] 的GitHub REPO 提供了眾多主題(Themes)可以選擇，可以先點選demo查看後，再決定要下載哪個喜歡的主題。下載檔案後解壓縮就是一個Jekyll的專案，修改專案內的個人資訊後(例如：Email、GitHub帳號等等)，可以搭配先前提到的指令:`jekyll serve` 查看Blog修改的內容是否與期望的相符合。

---
<h3 id="push_blog">上傳Blog</h3>
首先要有GitHub帳號，建立repository，將其屬性設定為GitHub Page。詳細操作內容可以看這裡[GitHub Pages][github_pages_url]。

接著使用下列指令即可將Blog上傳至GitHub repository囉。
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin youruser@yourserver.com:/path/to/my_project.git
git push origin master
```

接個就可以在 `https://username.github.io`[1]看到剛剛上傳的內容。

Note:    
[1] username為你在GitHub的帳號。

[newopen]: /blog_page/2017/11/06/new-open/
[jekyll_on_githubpage]: https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/
[jekyll]: https://jekyllrb.com/ "Jekyll Web Site."
[ruby]: https://www.ruby-lang.org/ "Ruby Web Site."
[gem]: http://guides.rubygems.org/what-is-a-gem/ "What is Gem?"
[rubyinstall_wins]: https://rubyinstaller.org/ "How to install ruby on windows?"
[rbenv]: https://github.com/rbenv/rbenv "How to install ruby on linux/UNIX?"
[RVM]: http://rvm.io/ "How to install ruby on linux/UNIX?"
[jekyll_init_photo]: ../../../../img/jekyll-init-page.png
[jekyll_themes]: https://github.com/jekyll/jekyll/wiki/Themes
[github_pages_url]: https://pages.github.com
