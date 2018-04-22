### 一、jQuery.i18n.properties
#### 1. jQuery.i18n.properties采用.properties文件对JavaScript进行国际化。
jQuery.i18n.properties插件首先加载默认的资源文件(filename.properties)，然后加载针对特定语言环境的资源文件(filename_zh.properties)，这就保证了在未提供某种语言的翻译时，默认值始终有效。
#### 2. filename的命名方式:
   filename.properties(默认定义)  
   filename_language.properties(自定义语言)  
   filename_language_country.properties(自定义语言加国家)  
#### 3. 应用说明：

```javascript
//引入jQuery.i18n.properties插件：
<script type="text/javascript" src="${basePath}/static/plugin/jqueryi18n/jquery.i18n.properties.min.js"></script>
/**
* Load and parse message bundle files (.properties),
* making bundles keys available as javascript variables.
*
* i18n files are named <name>.js, or <name>_<language>.js or <name>_<language>_<country>.js
* Where:
* The <language> argument is a valid ISO Language Code. These codes are the lower-case,
* two-letter codes as defined by ISO-639. You can find a full list of these codes at a
* number of sites, such as: http://www.loc.gov/standards/iso639-2/englangn.html
* The <country> argument is a valid ISO Country Code. These codes are the upper-case,
* two-letter codes as defined by ISO-3166. You can find a full list of these codes at a
* number of sites, such as: http://www.iso.ch/iso/en/prods-services/iso3166ma/02iso-3166-code-lists/list-en1.html
*
* Sample usage for a bundles/Messages.properties bundle:
* $.i18n.properties({
* name: 'Messages',
* language: 'en_US',
* path: 'bundles'
* });
* @param name (string/string[], optional) names of file to load (eg, 'Messages' or ['Msg1','Msg2']). Defaults to "Messages"
* @param language (string, optional) language/country code (eg, 'en', 'en_US', 'pt_BR'). if not specified, language reported by the browser will be used instead.
* @param path (string, optional) path of directory that contains file to load
* @param mode (string, optional) whether bundles keys are available as JavaScript variables/functions or as a map (eg, 'vars' or 'map')
* @param cache (boolean, optional) whether bundles should be cached by the browser, or forcibly reloaded on each page load. Defaults to false (i.e. forcibly reloaded)
* @param encoding (string, optional) the encoding to request for bundles. Property file resource bundles are specified to be in ISO-8859-1 format. Defaults to UTF-8 for backward compatibility.
* @param callback (function, optional) callback function to be called after script is terminated
*/ 
jQuery.i18n.properties({
    name : 'filename', //资源文件名称
    path : '${contentsPath}/static/i18n/', //资源文件路径
    mode : 'map', //用Map的方式使用资源文件中的值
    language : 'zh',
    callback : function() {//加载成功后设置显示内容
        console.info($.i18n.prop('key'));// key 为properties文件中定义的键值对相应的key，这样既数据key对应的value
    }
});

// filename.properties中定义的类容
// name=zivs_en

// filename_zh.properties中定义的类容
// name=zivs_zh

//使用
$("#my_input").val($.i18n.prop('name'));//获取key=name的value： $.i18n.prop('name')

```
#### 4. 总结：
若使用的filename.properties是项目的resource目录中已定义的话，需要将相应.properties文件copy一份到项目static目录(resource目录是在Service端，受保护，Client端不能直接获取)，这样也来同一份properties文件就会存在两份。


### 二、模板（Freemarker）嵌入使用
将资源国际化message定义成一个jQuery变量（在FreeMarker模板中），在将变量通过模板嵌入页面。这样页面就会存在一个动态引入的变量。
#### 1. 定义模板变量:
```javascript
//i18n配置文件 
//新建文件 syssetting_cn.properties和syssetting_en.properties分别如下：
syssetting_cn.properties
	sys.hint.timeout=请求超时
	sys.hint.optionFail=操作失败
	sys.hint.serviceException=服务异常
syssetting_en.properties
	sys.hint.timeout=Request Timeout
	sys.hint.optionFail=Operation Failed
	sys.hint.serviceException=Service Exception
//新建文件i18n_message.ftl 类容如下：
i18n_message
<#macro i18nMessage>
    var i18n_message = {
            serviceException : "<@spring.message code="sys.hint.serviceException"/>",
	    // <@spring.message code="sys.hint.serviceException"/>Spring i18n 的页面书写方式
            optionFail : "<@spring.message code="sys.hint.optionFail"/>",
            timeout : "<@spring.message code="sys.hint.timeout"/>"
        };
</#macro>

<#-- 在相应的页面引入freemarker宏模板 -->
<#import "i18n_message.ftl" as i18n_message />
```
#### 2. 页面引入模板变量：
```javascript
<#--页面引入定义的 i18n_message 资源数据-->
<@i18n_message.i18nMessage/>
```
#### 3. 获取值：
```javascript
<#--页面直接获取 i18n_message 定义的key为 serviceException 的值-->
console.info(i18n_message.serviceException);
```
#### 4. 总结：
filename.properties在系统中只存在唯一一份，保证了数据的唯一性。
