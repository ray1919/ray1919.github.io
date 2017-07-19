# CTNET站点维护日志

## 1. 网站框架

网站用的是Yii v1 [http://www.yiiframework.com/](http://www.yiiframework.com/)。

这个框架已经有v2，两个版本不一样，所以你如果要去网站看文档或搜索一些问题，注意Yii和Yii2是两个不一样的框。

这个框架是典型的Model-View-Controller结构。model为数据源，view为前端页面，controller为后端控制代码及router。


## 2. 网站结构

```
├── assets                 临时文件，需要设置服务器写权限
├── css 
├── files
├── images
├── protected
├── scripts
├── source
└── themes
```

protected目录下是代码
```
├── commands
│   └── shell
├── components
├── config                  网站的config, 包括数据库设置等
├── controllers             controller文件
├── data
├── extensions              Yii框架的扩展安装地方
│   ├── EDataTables
│   ├── EFullCalendar
│   ├── highcharts
│   ├── mbmenu
│   ├── prettify
│   ├── yii-mail
│   └── yiiword
├── messages
├── migrations              数据库migrations，没有用到这一特性
├── models                  model文件存放地方
├── modules
│   ├── media
│   └── userGroups
├── runtime
│   ├── gii-1.1.12
│   ├── gii-1.1.13
│   ├── gii-1.1.14
│   └── HTML
├── tests
│   ├── fixtures
│   ├── functional
│   ├── report
│   └── unit
├── vender
│   └── PHPWord
└── views                    view文件存放
    ├── customer
    ├── customerOrder
    ├── gene
    ├── layouts
    ├── mail
    ├── mirna
    ├── pCRExperiment
    ├── pCRSample
    ├── pCRService
    ├── plate
    ├── position
    ├── primer
    ├── report
    ├── sample
    ├── sampleUsageRecord
    ├── site
    ├── species
    ├── storeType
    ├── task
    ├── ugmail
    └── visit
```

需要修改前端页面就是修改对应的view文件，以report为例
```
├── admin.php
├── create.php
├── _form.php
├── index.php
├── _search.php
├── t.php
├── update.php
├── upload.php
├── upload.php.save
├── _view.php
└── view.php
```
index, admin, create, update, upload, view分别对应到controller中actionIndex, actionAdmin...

以最简单的一个动作为例
```php
        /**
         * Displays a particular model.
         * @param integer $id the ID of the model to be displayed
         */
        public function actionView($id)
        {
                $this->render('view',array(
                        'model'=>$this->loadModel($id),
                ));
        }
```
View动作接受一个id变量，从model获取数据，然后将数据传给view页面呈现出来。model就是对应到models文件夹下的```Report.php```，view就是对应到```views```文件夹下的```report/view.php```。

Yii有一个自动化模块Gii可以帮助产生一个空白的MVC模型，只要在数据库或migration先构建好数据表，他会根据数据表的各个列自动产生一个对应的模型以及对应的controller和view。然后我们再根据需要修改代码。

Gii模块的使用建议看个视频教程，就理解了。

## 3. 权限管理
权限管理并不是Yii框架自带，用的是他的一个extension - [userGroups](http://www.yiiframework.com/extension/usergroups)

登陆admin管理员账号后就可以进入管理界面，比较容易上手，不需要代码。

admin账号的密码我忘记了，你可以在数据库```usergroups_user```第一行admin用户的邮箱，然后输错密码后，进入密码恢复流程，回答问题也在数据表里能看到。

### 2017-06-13
### 赵锐


