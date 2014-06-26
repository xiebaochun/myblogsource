---
layout: post
title: "使用PHP和AJAX制作日历"
date: 2013-09-27 14:41
comments: true
categories: web
---
在很多网站，日历是一个必要的组成部分。通常这些日历是jquery的一个插件，但是也可以用php来实现。今天我将使用ajax技术展示一个月份的日历，可以通过左右箭头切换月份。由于是ajax,这个日历还有其他的优点，它是一个响应式的移动日历。在开始编写代码之前，我建议你先看看我们的demo.

![](http://www.script-tutorials.com/demos/361/php_ajax_calendar.png)

[在线演示](http://www.script-tutorials.com/demos/361/)
[下载源文件](http://www.script-tutorials.com/demos/361/source.zip)

---
文件结构：

在开始之前，让我们先为我们的文件定义一个清晰的文件夹结构：

- css-存放所有的css文件
- images-存放所有可能用到的图片
- templates-存放所有的模板文件
<!--more-->
### 第一步 编写HTML文件 ###

我们不使用任何特定的系统模板（如Smarty），我们将只使用我们自己写的一个简单的html模板。

templates/index.html

    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" />
    <title>PHP AJAX Calendar</title>
    
    <!-- add styles and scripts -->
    <link href="css/styles.css" rel="stylesheet" type="text/css" />
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.3.2/jquery.min.js"></script>
    </head>
    <body>
    <div id="calendar">
    __calendar__
    </div>
    </body>
    </html>

这个一个非常简单的index页面模板，我们在这定义了一个日历的父容器。另一个模板将会被用作日历的内部容器。

templates/celendar.html

    <div class="navigation">
    <a class="prev" href="index.php?month=__prev_month__" onclick="$('#calendar').load('index.php?month=__prev_month__&_r=' + Math.random()); return false;"></a>
    <div class="title" >__cal_caption__</div>
    <a class="next" href="index.php?month=__next_month__" onclick="$('#calendar').load('index.php?month=__next_month__&_r=' + Math.random()); return false;"></a>
    </div>
    
    <table>
    <tr>
    <th class="weekday">sun</th>
    <th class="weekday">mon</th>
    <th class="weekday">tue</th>
    <th class="weekday">wed</th>
    <th class="weekday">thu</th>
    <th class="weekday">fri</th>
    <th class="weekday">sat</th>
    </tr>
    __cal_rows__
    </table>

这是因为对于ajax的请求，我们不需要返回任何东西，除了内部的日历内容。

### 第二步 PHP ###

给日历添加行为

index.php

    // Get current year, month and day
    list($iNowYear, $iNowMonth, $iNowDay) = explode('-', date('Y-m-d'));
    
    // Get current year and month depending on possible GET parameters
    if (isset($_GET['month'])) {
    list($iMonth, $iYear) = explode('-', $_GET['month']);
    $iMonth = (int)$iMonth;
    $iYear = (int)$iYear;
    } else {
    list($iMonth, $iYear) = explode('-', date('n-Y'));
    }
    
    // Get name and number of days of specified month
    $iTimestamp = mktime(0, 0, 0, $iMonth, $iNowDay, $iYear);
    list($sMonthName, $iDaysInMonth) = explode('-', date('F-t', $iTimestamp));
    
    // Get previous year and month
    $iPrevYear = $iYear;
    $iPrevMonth = $iMonth - 1;
    if ($iPrevMonth <= 0) {
    $iPrevYear--;
    $iPrevMonth = 12; // set to December
    }
    
    // Get next year and month
    $iNextYear = $iYear;
    $iNextMonth = $iMonth + 1;
    if ($iNextMonth > 12) {
    $iNextYear++;
    $iNextMonth = 1;
    }
    
    // Get number of days of previous month
    $iPrevDaysInMonth = (int)date('t', mktime(0, 0, 0, $iPrevMonth, $iNowDay, $iPrevYear));
    
    // Get numeric representation of the day of the week of the first day of specified (current) month
    $iFirstDayDow = (int)date('w', mktime(0, 0, 0, $iMonth, 1, $iYear));
    
    // On what day the previous month begins
    $iPrevShowFrom = $iPrevDaysInMonth - $iFirstDayDow + 1;
    
    // If previous month
    $bPreviousMonth = ($iFirstDayDow > 0);
    
    // Initial day
    $iCurrentDay = ($bPreviousMonth) ? $iPrevShowFrom : 1;
    
    $bNextMonth = false;
    $sCalTblRows = '';
    
    // Generate rows for the calendar
    for ($i = 0; $i < 6; $i++) { // 6-weeks range
    $sCalTblRows .= '<tr>';
    for ($j = 0; $j < 7; $j++) { // 7 days a week
    
    $sClass = '';
    if ($iNowYear == $iYear && $iNowMonth == $iMonth && $iNowDay == $iCurrentDay && !$bPreviousMonth && !$bNextMonth) {
    $sClass = 'today';
    } elseif (!$bPreviousMonth && !$bNextMonth) {
    $sClass = 'current';
    }
    $sCalTblRows .= '<td class="'.$sClass.'"><a href="javascript: void(0)">'.$iCurrentDay.'</a></td>';
    
    // Next day
    $iCurrentDay++;
    if ($bPreviousMonth && $iCurrentDay > $iPrevDaysInMonth) {
    $bPreviousMonth = false;
    $iCurrentDay = 1;
    }
    if (!$bPreviousMonth && !$bNextMonth && $iCurrentDay > $iDaysInMonth) {
    $bNextMonth = true;
    $iCurrentDay = 1;
    }
    }
    $sCalTblRows .= '</tr>';
    }
    
    // Prepare replacement keys and generate the calendar
    $aKeys = array(
    '__prev_month__' => "{$iPrevMonth}-{$iPrevYear}",
    '__next_month__' => "{$iNextMonth}-{$iNextYear}",
    '__cal_caption__' => $sMonthName . ', ' . $iYear,
    '__cal_rows__' => $sCalTblRows,
    );
    $sCalendarItself = strtr(file_get_contents('templates/calendar.html'), $aKeys);
    
    // AJAX requests - return the calendar
    if (isset($_SERVER['HTTP_X_REQUESTED_WITH']) && $_SERVER['HTTP_X_REQUESTED_WITH'] == 'XMLHttpRequest' && isset($_GET['month'])) {
    header('Content-Type: text/html; charset=utf-8');
    echo $sCalendarItself;
    exit;
    }
    
    $aVariables = array(
    '__calendar__' => $sCalendarItself,
    );
    echo strtr(file_get_contents('templates/index.html'), $aVariables);

我尽量为每行代码都加上了注释，目的是为了让你更加容易理解整个处理过程。开始我们创建了日历相关的当前日期很请求日期。然后我们生成日历表格，最后我们将模板的关键字替换掉。在Ajax请求时，我们只翻译日历内部的内容($sCalendarItself)，否则我们将显示整个页面。

### 第三步 CSS ###

现在，我们的日历看起来不那么美观，因为只有html代码。让我们装饰一下我们的日历吧！

css/style.css

    /* calendar styles */
    #calendar {
    -moz-user-select: none;
    border: 1px solid #EEEEEE;
    border-radius: 6px 6px 6px 6px;
    color: #333333;
    font-family: Arial,sans-serif;
    font-size: 1.1em;
    margin: 10px auto;
    padding: 0.4em;
    width: 90%;
    }
    #calendar .navigation {
    background-color: #CC0000;
    border: 1px solid #E3A1A1;
    border-radius: 6px 6px 6px 6px;
    color: #FFFFFF;
    font-weight: bold;
    padding: 1px;
    position: relative;
    }
    #calendar .navigation .title {
    background: none repeat scroll 0 0 transparent;
    border-color: rgba(0, 0, 0, 0);
    color: inherit;
    line-height: 1.8em;
    margin: 0 2.3em;
    text-align: center;
    }
    #calendar .navigation .prev, #calendar .navigation .next {
    background-image: url(../images/nav.png);
    height: 24px;
    opacity: 0.9;
    position: absolute;
    top: 4px;
    width: 24px;
    }
    #calendar .navigation .prev {
    background-position: 0 0;
    left: 4px;
    }
    #calendar .navigation .next {
    background-position: -24px 0;
    right: 4px;
    }
    #calendar .navigation .prev:hover, #calendar .navigation .next:hover {
    opacity: 1;
    }
    #calendar table {
    border-collapse: collapse;
    font-size: 0.9em;
    table-layout: fixed;
    width: 100%;
    }
    #calendar table th {
    border: 0 none;
    font-weight: bold;
    padding: 0.7em 0.3em;
    text-align: center;
    }
    #calendar table td {
    border: 0 none;
    padding: 1px;
    }
    #calendar table td a {
    background-color: #EEEEEE;
    border: 1px solid #D8DCDF;
    color: #004276;
    display: block;
    font-weight: normal;
    opacity: 0.7;
    padding: 0.2em;
    text-align: right;
    text-decoration: none;
    }
    #calendar table td a:hover {
    background-color: #F6F6F6;
    border: 1px solid #CDD5DA;
    color: #111111;
    }
    #calendar table td.current a {
    font-weight: bold;
    opacity: 1;
    }
    #calendar table td.today a {
    background-color: #FBF8EE;
    border: 1px solid #FCD3A1;
    color: #444444;
    font-weight: bold;
    opacity: 1;
    }

### 第四步 图片 ###

这里只要使用一个很小的导航图片就行了

![](http://www.script-tutorials.com/demos/361/images/nav.png)

### 总结 ###

这就是今天的所有内容，我们只是准备了响应式风格的日历。感谢你耐心看完本文，如果你喜欢的话，请使用下面的社交工具将它分享给你的朋友吧！

原文地址：[http://www.script-tutorials.com/php-ajax-calendar/](http://www.script-tutorials.com/php-ajax-calendar/)
