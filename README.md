# Extend TimeZoneName Option Proposal

<!--
 
  1.  Add a post-rewrite git hook to auto-rebuild the output on every commit:
      ```sh
      cp hooks/post-rewrite .git/hooks/post-rewrite
      chmod +x .git/hooks/post-rewrite
      ```
-->
## Stage 
Stage 3

* Advanced to [Stage 1](https://docs.google.com/presentation/d/1G8XTK4tjzXxJAs6GpZvrd6SjxL40h_fgrEq9CN6jDPw/edit#slide=id.p) in [TC39 2021-01-25~28 meeting](https://github.com/tc39/agendas/blob/master/2021/01.md).
* 2021-04-08 ECMA402 meeting agree to proceed to TC39 for Stage 2
  * Rename (short|long)GMT to (short|long)Offset 
*  Advanced to [Stage 2](https://docs.google.com/presentation/d/1eYuygaDvErbftDjg4S6zCYqhaxpEiCyFDDdpjmPaMyo/) in [TC39 2021-04 meeting](https://github.com/tc39/agendas/blob/master/2021/04.md).
  * Philip Chimento and Rick Button (assisted by Ujjwal) signed up to be Stage 3 reviewers
*  Advanced to [Stage 3](https://docs.google.com/presentation/d/1N4QoCxFVM4ZKr9gDnaDDnrHb-5_rPTy-wydp-f90xBM) in [TC39 2021-05 meeting](https://github.com/tc39/agendas/blob/master/2021/05.md).
*  Update during  [Stage 3](https://docs.google.com/presentation/d/1a4cp-Jw_k47iv3oHFDC2rhkNPHbZbTzrZOSVR_4_QlM/edit#slide=id.p) in [TC39 2021-07 meeting](https://github.com/tc39/agendas/blob/master/2021/07.md)
*  Slide for [Stage 4 Advancement for TC39 2021-Dec meeting](https://docs.google.com/presentation/d/1VGz9AlDRqITk3MiKmacgibMSBCo8qN3LZs4BeqNDQsI/) 



## Champion
* Frank Tang @FrankYFTang

## Stage 3 reviewers (needed during stage 2)
* Philip Chimento @ptomato
* Rick Button @rickbutton 

## Editor
* TBD

## Scope
  Extend the timeZoneName option in Intl.DateTimeFormat object to support more formatted options.
  
## Background
Prior Discussion: [ECMA402#119 DateTimeFormat: consider adding more timezone display options](https://github.com/tc39/ecma402/issues/119)
 
Ref: [UTS35 - "7 Using Time Zone Names"](http://unicode.org/reports/tr35/tr35-dates.html#Using_Time_Zone_Names)
##  Updated Proposal - add 4 new formats only
| timeZoneName | CLDR pattern | Examples | Description in UTS35 | ICU key | Total # of items in 476 locales| Total bytes in UTF8 | Compressed Size |
| --- | --- |--- | --- | --- | --- | --- | --- |
| shortOffset | O | GMT-8  | The short localized GMT format. | zone/\*/zoneStrings/gmt\.\*Format | 263 | 1826 | 392 |
| longOffset  | OOOO | GMT-0800 | The long localized GMT format. | (same as above) | (no extra from above) | (no extra from above) | (no extra from above) |
| shortGeneric  | v | PT | The short generic non-location format. | zone/\*/zoneStrings/meta:\*/sg | 332  | 1719  |  311 | 
| longGeneric | vvvv | Pacific Time | The long generic non-location format. | zone/\*/zoneStrings/meta:\*/lg | 10047 | 278103 | 69526 |

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
## Tracking
* [Mozilla Tracking Bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1710429)
* [V8 Tracking Bug](https://bugs.chromium.org/p/v8/issues/detail?id=11661)
  * V8 is now implemented behind the flag --harmony_intl_more_timezone

### Output from V8  of the updated proposal

```
$ out/x64.release/d8 --harmony_intl_more_timezone
V8 version 8.9.0 (candidate)
d8> let timeZoneNames = ["short", "long", "shortOffset", "longOffset", "shortGeneric", "longGeneric"];
d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName}))});
d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName}))});
short: 12:27:10 PM PST
long: 12:27:10 PM Pacific Standard Time
shortOffset: 12:27:10 PM GMT-8
longOffset: 12:27:10 PM GMT-08:00
shortGeneric: 12:27:10 PM PT
longWall: 12:27:10 PM Pacific Time


d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("zh-Hant", {timeZoneName}))});
short: 下午12:26:20 [PST]
long: 下午12:26:20 [太平洋標準時間]
shortOffset: 下午12:26:20 [GMT-8]
longOffset: 下午12:26:20 [GMT-08:00]
shortGeneric: 下午12:26:20 [PT]
longGeneric: 下午12:26:20 [太平洋時間]


d8> timeZoneNames.forEach(function(timeZoneName) { print(timeZoneName + ": " + (new Date()).toLocaleTimeString("en", {timeZoneName, timeZone: "Asia/Calcutta"}))});
short: 1:54:56 AM GMT+5:30
long: 1:54:56 AM India Standard Time
shortOffset: 1:54:56 AM GMT+5:30
longOffset: 1:54:56 AM GMT+05:30
shortGeneric: 1:54:56 AM India Time
longGeneric: 1:54:56 AM India Standard Time

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
