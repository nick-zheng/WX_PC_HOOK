**electron+vue**

Electron中文网：
https://electron.org.cn/vue/index.html

# 安装 vue-cli 和 脚手架样板代码
sudo npm install -g vue-cli
vue init simulatedgreg/electron-vue my-project

# 安装依赖并运行你的程序
cd my-project
yarn # 或者 npm install
yarn run dev # 或者 npm run dev




electron程序，如何利用appdmg打包生成dmg安装包？
https://newsn.net/say/electron-appdmg.html

electron-packager ./ appname --platform=win32 --arch=x64 --win32metadata.FileDescription='' --overwrite --ignore=node_modules/electron-* --ignore=node_modules/.bin --ignore=.git --ignore=out --no-prune --electron-version=1.7.9 --out=out --icon=assets/app-icon/win/app.ico --asar


"packager:all": "electron-packager ./ --appname=YXEditorTool --out=./output --app-version=1.0.0 --overwrite --ignore=./node_modules/electron-* --ignore=./node_modules/.bin --ignore=.git --ignore=output --no-prune --icon=./build/icons/icon.ico --platform=all ",
"packager:darwin": "electron-packager ./ --appname=YXEditorTool --out=./output --app-version=1.0.0 --overwrite --ignore=./node_modules/electron-* --ignore=./node_modules/.bin --ignore=.git --ignore=output --no-prune --icon=./build/icons/icon.ico --platform=darwin",
"packager:linux": "electron-packager ./ --appname=YXEditorTool --out=./output --app-version=1.0.0 --overwrite --ignore=./node_modules/electron-* --ignore=./node_modules/.bin --ignore=.git --ignore=output --no-prune --icon=./build/icons/icon.ico --platform=linux",
"packager:mac": "electron-packager ./ --appname=YXEditorTool --out=./output --app-version=1.0.0 --overwrite --ignore=./node_modules/electron-* --ignore=./node_modules/.bin --ignore=.git --ignore=output --no-prune --icon=./build/icons/icon.ico --platform=mas",
"packager:win32": "electron-packager ./ --appname=YXEditorTool --out=./output --app-version=1.0.0 --overwrite --ignore=./node_modules/electron-* --ignore=./node_modules/.bin --ignore=.git --ignore=output --no-prune --icon=./build/icons/icon.ico --platform=win32",
"dmg:clean": "rm -f ./output/YXEditorTool.dmg",
"dmg": "rm -f ./output/YXEditorTool.dmg && appdmg ./build/dmg/dmg.json ./output/YXEditorTool.dmg"



#安装appdmg(github:https://github.com/LinusU/node-appdmg)
sudo npm install -g appdmg --unsafe-perm

#查看版本
appdmg --version

#准备相关文件：
#1、编写dmg.json配置文件
{
    "title": "MyApp",
    "icon": "icon.icns",
    "background": "bg.png",
    "contents": [
      { "x": 380, "y": 170, "type": "link", "path": "/Applications" }, //y指的是图标中心点的位置（通常设置为高度的一半再减去一行文字的偏移量）
      { "x": 200, "y": 170, "type": "file", "path": "./../electron-quick-start-darwin-x64/electron-quick-start.app" }
    ],
    "window": {
        "size": {
            "width": 580,  //就是bg.png的宽度
            "height": 360  //就是bg.png的高度
        }
    },
    "format": "UDBZ" //压缩格式
}

#2、icon.icns图标文件，制作方式：https://newsn.net/say/electron-icns.html
	在/etc/profile中添加：
	#参考：https://blog.csdn.net/qq_37261367/article/details/81128276
#create icon
alias icon_16='sips -z 16 16 icon.png --out icon.iconset/icon_16x16.png'
alias icon_16_2='sips -z 32 32 icon.png --out icon.iconset/icon_16x16@2x.png'
alias icon_16_3='sips -z 48 48 icon.png --out icon.iconset/icon_16x16@3x.png'

alias icon_32='sips -z 32 32 icon.png --out icon.iconset/icon_32x32.png'
alias icon_32_2='sips -z 64 64 icon.png --out icon.iconset/icon_32x32@2x.png'
alias icon_32_3='sips -z 96 96 icon.png --out icon.iconset/icon_32x32@3x.png'

alias icon_64='sips -z 64 64 icon.png --out icon.iconset/icon_64x64.png'
alias icon_64_2='sips -z 128 128 icon.png --out icon.iconset/icon_64x64@2x.png'
alias icon_64_3='sips -z 192 192 icon.png --out icon.iconset/icon_64x64@3x.png'

alias icon_128='sips -z 128 128 icon.png --out icon.iconset/icon_128x128.png'
alias icon_128_2='sips -z 256 256 icon.png --out icon.iconset/icon_128x128@2x.png'
alias icon_128_3='sips -z 384 384 icon.png --out icon.iconset/icon_128x128@3x.png'

alias icon_256='sips -z 256 256 icon.png --out icon.iconset/icon_256x256.png'
alias icon_256_2='sips -z 512 512 icon.png --out icon.iconset/icon_256x256@2x.png'
alias icon_256_3='sips -z 768 768 icon.png --out icon.iconset/icon_256x256@3x.png'

alias icon_512='sips -z 512 512 icon.png --out icon.iconset/icon_512x512.png'
alias icon_512_2='sips -z 1024 1024 icon.png --out icon.iconset/icon_512x512@2x.png'
alias icon_512_3='sips -z 1536 1536 icon.png --out icon.iconset/icon_512x512@3x.png'

alias icon_1024='sips -z 1024 1024 icon.png --out icon.iconset/icon_1024x1024.png'

###iPhone
alias icon_29='sips -z 29 29 icon.png --out icon.iconset/icon_29x29.png'
alias icon_29_2='sips -z 58 58 icon.png --out icon.iconset/icon_29x29@2x.png'
alias icon_29_3='sips -z 87 87 icon.png --out icon.iconset/icon_29x29@3x.png'

alias icon_40='sips -z 40 40 icon.png --out icon.iconset/icon_40x40.png'
alias icon_40_2='sips -z 80 80 icon.png --out icon.iconset/icon_40x40@2x.png'
alias icon_40_3='sips -z 120 120 icon.png --out icon.iconset/icon_40x40@3x.png'

alias icon_57='sips -z 57 57 icon.png --out icon.iconset/icon_57x57.png'
alias icon_57_2='sips -z 114 114 icon.png --out icon.iconset/icon_57x57@2x.png'
alias icon_57_3='sips -z 171 171 icon.png --out icon.iconset/icon_57x57@3x.png'

alias icon_60='sips -z 60 60 icon.png --out icon.iconset/icon_60x60.png'
alias icon_60_2='sips -z 120 120 icon.png --out icon.iconset/icon_60x60@2x.png'
alias icon_60_3='sips -z 180 180 icon.png --out icon.iconset/icon_60x60@3x.png'
###iPhone

alias icons_16='icon_16 && icon_16_2 && icon_16_3'
alias icons_32='icon_32 && icon_32_2 && icon_32_3'
alias icons_64='icon_64 && icon_64_2 && icon_64_3'
alias icons_128='icon_128 && icon_128_2 && icon_128_3'
alias icons_256='icon_256 && icon_256_2 && icon_256_3'
alias icons_512='icon_512 && icon_512_2 && icon_512_3'
alias icons_1024='icon_1024'
alias create_icons_desktop='icons_16 && icons_32 && icons_64 && icons_128 && icons_256 && icons_512 && icons_1024'
###iPhone
alias icons_29='icon_29 && icon_29_2 && icon_29_3'
alias icons_40='icon_40 && icon_40_2 && icon_40_3'
alias icons_57='icon_57 && icon_57_2 && icon_57_3'
alias icons_60='icon_60 && icon_60_2 && icon_60_3'
alias create_icons_iphone='icons_29 && icons_40 && icons_57 && icons_60'
###iPhone
alias create_icons='create_icons_desktop && create_icons_iphone'


alias create_iconset='mkdir icon.iconset'

alias iconset_2_icns='iconutil -c icns icon.iconset -o icon.icns'

alias pngicon_2_icns='create_iconset && create_icons && iconset_2_icns'

alias iphone_iconset='create_iconset && create_icons_iphone'


	#运行命令：
	pngicon_2_icns
#3、MyApp.app可执行文件，制作方式：https://newsn.net/say/electron-packager.html
 rimraf outÂ &&cross-env NODE_ENV=productionÂ &&electron-packager ./ appname --platform=win32 --arch=x64 --win32metadata.FileDescription=''--overwrite --ignore=node_modules/electron-* --ignore=node_modules/.bin --ignore=.git --ignore=out --no-prune --electron-version=1.7.9 --out=out --icon=assets/app-icon/win/app.ico --asar

	npm install rimraf cross-env electron-packager -g --save-dev

	或者：
	electron-builder —win —ia32 —dir

#4、bg.png背景图片

#5、目录结构：在项目里与package.json文件同级目录下创建dmg文件夹，存放#1、#2、#3、#4的文件

#6、生成dmg（命令格式：appdmg <json文件路径> <dmg文件路径>)
#在package.json中scripts中配置:
"dmg": "rm -f ./dmg/MyApp.dmg && appdmg ./dmg/dmg.json ./dmg/MyApp.dmg"

#执行：
npm run dmg


