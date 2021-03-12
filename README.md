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
* Plan to propose to advance to Stage 2 in [TC39 2021-04 meeting](https://github.com/tc39/agendas/blob/master/2021/04.md).


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
