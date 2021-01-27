# Extend TimeZoneName Option Proposal

<!--
 
  1.  Add a post-rewrite git hook to auto-rebuild the output on every commit:
      ```sh
      cp hooks/post-rewrite .git/hooks/post-rewrite
      chmod +x .git/hooks/post-rewrite
      ```
-->
## Stage 
Stage 1

* Plan to propose to advance to Stage 1 in [TC39 2021-01-25~28 meeting](https://github.com/tc39/agendas/blob/master/2021/01.md).
  * Advanced to Stage 1


## Champion
* Frank Tang @FrankYFTang

## Scope
  Extend the timeZoneName option in Intl.DateTimeFormat object to support more formatted options.
  
## Background
Prior Discussion: [ECMA402#119 DateTimeFormat: consider adding more timezone display options](https://github.com/tc39/ecma402/issues/119)
 
Ref: [UTS35 - "7 Using Time Zone Names"](http://unicode.org/reports/tr35/tr35-dates.html#Using_Time_Zone_Names)
##  Updated Proposal - add 4 new formats only
| timeZoneName | CLDR pattern | Examples | Description in UTS35 | ICU key | Total # of items in 476 locales| Total bytes in UTF8 | Compressed Size |
| --- | --- |--- | --- | --- | --- | --- | --- |
| shortGMT | O | GMT-8  | The short localized GMT format. | zone/\*/zoneStrings/gmt\.\*Format | 263 | 1826 | 392 |
| longGMT  | OOOO | GMT-0800 | The long localized GMT format. | (same as above) | (no extra from above) | (no extra from above) | (no extra from above) |
| shortWall  | v | PT | The short generic non-location format. | zone/\*/zoneStrings/meta:\*/sg | 332  | 1719  |  311 | 
| longWall | vvvv | Pacific Time | The long generic non-location format. | zone/\*/zoneStrings/meta:\*/lg | 10047 | 278103 | 69526 |

Note:
Here is how I get the above number

```
$ cd icu/icu4c/source/data/zone
$ egrep "sg\{" *|cut -d\" -f2 |wc
    332     333    1719
$ egrep "sg\{" *|cut -d\" -f2 | gzip -f |wc -c
311
$ egrep "lg\{" *|cut -d\" -f2 |wc 
  10047   24026  278103 
$ egrep "lg\{" *|cut -d\" -f2 | gzip -f |wc -c
69526
$ egrep "gmt.*Format\{" *|cut -d\" -f2 |wc
    263     284    1826
$ egrep "gmt.*Format\{" *|cut -d\" -f2 | gzip -f |wc -c
392
```

### Output from V8 prototype of the updated proposal
v8 prototype: https://chromium-review.googlesource.com/c/v8/v8/+/2425328
```
$ out/x64.release/d8 --harmony_intl_more_timezone
V8 version 8.9.0 (candidate)
d8> let timeZoneNames = ["short", "long", "shortGMT", "longGMT", "shortWall", "longWall"];
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("en", {timeZoneName}))});
9:27:14 AM PST
9:27:14 AM Pacific Standard Time
9:27:14 AM GMT-8
9:27:14 AM GMT-08:00
9:27:14 AM PT
9:27:14 AM Pacific Time
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("zh-Hant", {timeZoneName}))});
上午9:27:27 [PST]
上午9:27:27 [太平洋標準時間]
上午9:27:27 [GMT-8]
上午9:27:27 [GMT-08:00]
上午9:27:27 [PT]
上午9:27:27 [太平洋時間]
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("fr", {timeZoneName}))});
9:27:47 UTC−8
9:27:47 heure normale du Pacifique nord-américain
09:27:47 UTC−8
09:27:47 UTC−08:00
09:27:47 heure : Los Angeles
09:27:47 heure du Pacifique nord-américain
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("ko", {timeZoneName}))});
오전 9시 28분 23초 GMT-8
오전 9시 28분 23초 북미 태평양 표준시
오전 9시 28분 23초 GMT-8
오전 9:28:23 GMT-08:00
오전 9:28:23 Los Angeles 시간
오전 9:28:23 북미 태평양 시간
undefined

```



## Copy from https://github.com/tc39/ecma402/issues
Straw Proposal: ([Proposed by @srl295  on Sep 22, 2020](https://github.com/tc39/ecma402/issues/119#issuecomment-696887550))
| timeZoneName | CLDR pattern | Examples | Note |
| --- | --- |--- | --- |
| short | z | PST  | Supported by ECMA402 already |
| long | zzzz | Pacific Standard Time  | Supported by ECMA402 already |
| offset | Z | -0800  | Frank think this is for toISOString not good for Intl.DateTimeFormat |
| longOffset  | ZZZZ | GMT-0800 |  |
| shortWall  | v | PT |  |
| longWall | vvvv | Pacific Time |  |
| location | VVV |	Los Angeles | Require a lot of extra data (need 664K of extra data [196K compressed] ) |
| longLocation | VVVV | Los Angeles TIme | Require a lot of extra data (same as above)|
| locode | V | uslax |  Frank oppose to support this|
| id | VV	| America/Los_Angeles | Frank oppose to support this |

How to estimate the required data:
```
$ cd icu/icu4c/source/data/zone
$ egrep "ec\{" *|cut -d\" -f2 |wc
  45924   53835  664784
$ egrep "ec\{" *|cut -d\" -f2 | gzip -f |wc -c
196639

```

### Output from V8 prototype of the strawman proposal
v8 prototype: https://chromium-review.googlesource.com/c/v8/v8/+/2425328/1

Notice it is under the data filtered out data for location and longLocation so the prototype does not show localized values but English only for location and longLocation:
```
$ out/x64.release/d8 --harmony_intl_more_timezone
V8 version 8.7.0 (candidate)
d8> let timeZoneNames = ["short", "long", "offset", "longOffset", "shortWall", "longWall", "locode", "id", "location", "longLocation"];
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("en", {timeZoneName}))});
5:39:14 PM PDT
5:39:14 PM Pacific Daylight Time
5:39:14 PM -0700
5:39:14 PM GMT-07:00
5:39:14 PM PT
5:39:14 PM Pacific Time
5:39:14 PM uslax
5:39:14 PM America/Los_Angeles
5:39:14 PM Los Angeles
5:39:14 PM Los Angeles Time
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("zh-Hant", {timeZoneName}))});
下午5:39:40 [PDT]
下午5:39:40 [太平洋夏令時間]
下午5:39:40 [-0700]
下午5:39:40 [GMT-07:00]
下午5:39:40 [PT]
下午5:39:40 [太平洋時間]
下午5:39:40 [uslax]
下午5:39:40 [America/Los_Angeles]
下午5:39:40 [Los Angeles]
下午5:39:40 [Los Angeles時間]
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("fr", {timeZoneName}))});
17:40:55 UTC−7
17:40:55 heure d’été du Pacifique
17:40:55 -0700
17:40:55 UTC−07:00
17:40:55 heure : Los Angeles
17:40:55 heure du Pacifique nord-américain
17:40:55 uslax
17:40:55 America/Los_Angeles
17:40:55 Los Angeles
17:40:55 heure : Los Angeles
undefined
d8> timeZoneNames.forEach(function(timeZoneName) { print((new Date()).toLocaleTimeString("ko", {timeZoneName}))});
오후 5시 41분 31초 GMT-7
오후 5시 41분 31초 북미 태평양 하계 표준시
오후 5시 41분 31초 -0700
오후 5:41:31 GMT-07:00
오후 5:41:31 Los Angeles 시간
오후 5:41:31 북미 태평양 시간
오후 5시 41분 31초 uslax
오후 5:41:31 America/Los_Angeles
오후 5:41:31 Los Angeles
오후 5:41:31 Los Angeles 시간
undefined
```


## To be deleted ...
  3.  ["How to write a good explainer"][explainer] explains how to make a good first impression.

      > Each TC39 proposal should have a `README.md` file which explains the purpose
      > of the proposal and its shape at a high level.
      >
      > ...
      >
      > The rest of this page can be used as a template ...

      Your explainer can point readers to the `index.html` generated from `spec.emu`
      via markdown like

      ```markdown
      You can browse the [ecmarkup output](https://ACCOUNT.github.io/PROJECT/)
      or browse the [source](https://github.com/ACCOUNT/PROJECT/blob/HEAD/spec.emu).
      ```

      where *ACCOUNT* and *PROJECT* are the first two path elements in your project's Github URL.
      For example, for github.com/**tc39**/**template-for-proposals**, *ACCOUNT* is "tc39"
      and *PROJECT* is "template-for-proposals".


## Maintain your proposal repo

  1. Make your changes to `spec.emu` (ecmarkup uses HTML syntax, but is not HTML, so I strongly suggest not naming it ".html")
  1. Any commit that makes meaningful changes to the spec, should run `npm run build` and commit the resulting output.
  1. Whenever you update `ecmarkup`, run `npm run build` and commit any changes that come from that dependency.

  [explainer]: https://github.com/tc39/how-we-work/blob/HEAD/explainer.md
