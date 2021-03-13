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

* Advanced to [Stage 1](https://docs.google.com/presentation/d/1G8XTK4tjzXxJAs6GpZvrd6SjxL40h_fgrEq9CN6jDPw/edit#slide=id.p) in [TC39 2021-01-25~28 meeting](https://github.com/tc39/agendas/blob/master/2021/01.md).
* Plan to propose to advance to [Stage 2](https://docs.google.com/presentation/d/1eYuygaDvErbftDjg4S6zCYqhaxpEiCyFDDdpjmPaMyo/) in [TC39 2021-04 meeting](https://github.com/tc39/agendas/blob/master/2021/04.md).


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
v8 prototype: https://chromium-review.googlesource.com/c/v8/v8/+/2757899

$ out/x64.release/d8 --harmony_intl_more_timezone
V8 version 8.9.0 (candidate)
d8> let timeZoneNames = ["short", "long", "shortGMT", "longGMT", "shortWall", "longWall"];
d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName}))});
d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName}))});
short: 12:27:10 PM PST
long: 12:27:10 PM Pacific Standard Time
shortGMT: 12:27:10 PM GMT-8
longGMT: 12:27:10 PM GMT-08:00
shortWall: 12:27:10 PM PT
longWall: 12:27:10 PM Pacific Time


d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("zh-Hant", {timeZoneName}))});
short: 下午12:26:20 [PST]
long: 下午12:26:20 [太平洋標準時間]
shortGMT: 下午12:26:20 [GMT-8]
longGMT: 下午12:26:20 [GMT-08:00]
shortWall: 下午12:26:20 [PT]
longWall: 下午12:26:20 [太平洋時間]

d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("fr", {timeZoneName}))});
short: 12:25:48 UTC−8
long: 12:25:48 heure normale du Pacifique nord-américain
shortGMT: 12:25:48 UTC−8
longGMT: 12:25:48 UTC−08:00
shortWall: 12:25:48 heure : Los Angeles
longWall: 12:25:48 heure du Pacifique nord-américain


d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("ko", {timeZoneName}))});
short: 오후 12시 23분 41초 GMT-8
long: 오후 12시 23분 41초 북미 태평양 표준시
shortGMT: 오후 12시 23분 41초 GMT-8
longGMT: 오후 12:23:41 GMT-08:00
shortWall: 오후 12:23:41 Los Angeles 시간
longWall: 오후 12:23:41 북미 태평양 시간

d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName, timeZone: "Asia/Calcutta"}))});
short: 1:54:56 AM GMT+5:30
long: 1:54:56 AM India Standard Time
shortGMT: 1:54:56 AM GMT+5:30
longGMT: 1:54:56 AM GMT+05:30
shortWall: 1:54:56 AM India Time
longWall: 1:54:56 AM India Standard Time



```
See ~possible options removed due to size concern~ in [archived README.md](https://github.com/tc39-transfer/proposal-intl-extend-timezonename/blob/main/README-old.md)

## Stage Advancement Planning

### Entrance Criteria For Stage 2
* Initial spec text  https://tc39.es/proposal-intl-extend-timezonename/ DONE
* All Entrance Criteria for State 1 DONE
* **Acceptance Signifies:** “The committee expects the feature to be developed and eventually included in the standard”

### Entrance Criteria For Stage 1
* Identified “champion” who will advance the addition: DONE- @FrankYFTang
* Prose outlining the problem or need and the general shape of a solution DONE
* Illustrative examples of usage DONE
* High-level API DONE
* Discussion of key algorithms, abstractions and semantics DONE
* Identification of potential “cross-cutting” concerns and implementation challenges/complexity DONE
* A publicly available repository for the proposal that captures the above requirements: DONE  https://github.com/tc39/proposal-intl-extend-timezonename/ 
* **Acceptance Signifies:** “The committee expects to devote time to examining the problem space, solutions and cross-cutting concerns”
