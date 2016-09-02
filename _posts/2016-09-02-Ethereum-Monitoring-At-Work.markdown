---
published: true
title: Ethermine API scraper written in Powershell
layout: post
tags: [life, ocd, Optimization, analytics, perspective]
---

While I don't know how many of you use ethermine.org, I know they have been good to me. I wrote a nice little powershell script to run on-demand and to get an idea of where my profits stand. This script also hits Etherscan.io to look at my balance and Coingecko.com to get the current exchange rate and estimate $USD/hour, day, month, etc profits.

First, "Da da da daaaaa", here's my happy little mining rig.
![HOT](https://dl.dropboxusercontent.com/u/2959356/blog/img_thermal_1458913467210.jpg)

Well, that's a thermal image of it. You see, for those who don't know, Ethereium is "made" by doing excessive computation with high-power video cards. Running a computer this hard generates a pile of heat. When we first turned this machine on, it was still in the tail of winter. Let's just say that we used less than half the oil we normally do for the entire house.

Here's the actual box.
![box](https://dl.dropboxusercontent.com/u/2959356/blog/P_20160312_154019.jpg)
Excuse the mess. Things often get hectic on my various workbenches.

So, to get started, ethermine offers a wonderful little api that is accessible via `"http://ethermine.org/api/miner_new/<youraccountnumberhere>"` If you browse to that address, you get a big screen full of text that looks similar to a spreadsheet given a large enough screen. What you are looking at, is the actual data that ethermine.org uses to build up its own graphs and diagrams. Let's look at the available parameters. Since we will be using it a lot, let's set up a variable for our Etherium wallet number. For the purpose of this script, we will use the current top miner on ethermine.org. This all can be done in powershell using Invoke-RestMethod like so:

```Powershell
$ETHACCTNUMBER=ec9ec16cd0725cdbfe3f6d9e449d7eadd50b6aee
$ethermineweb_API = "http://ethermine.org/api/miner_new/$ETHACCTNUMBER"
$etherminejson = Invoke-RestMethod -Uri $ethermineweb_API
```

<div  markdown="1" style="float: right ;margin: 7px">
<img src="https://dl.dropboxusercontent.com/u/2959356/blog/etherminejson.gif" alt="ethermine json example animation.">
</div>

This sets the $etherminejson to all the available data from the API. Invoke-RestMethod is quite nice, it will auto-parse a json api for us to use like so.

Well that's neat. Look, we can access every parameter we might need simply by addressing $etherminejson.<thingy>. And, ass you can see, he's very smart and usually leaves less than a penny in his mining wallet. This is definitely best practice. Add a layer or two of abstraction from your public-facing wallet number to face less risk of theft or bombardment.

Let's set up another one of these for the etherscan API. Same trick as before, using Invoke-RestMethod.

```powershell
$totalcoinjson = Invoke-RestMethod -Uri $totalcoinweb_API
```

As you can see, $totalcoinjson.result gives us our eth total x10 ^37! Neat! Let's clean that up to be human readable, and also add in our "waiting to be payed out balance." from ethermine.

```powershell
$totalcoinweb_API = "http://api.etherscan.io/api?module=account&action=balance&address=0x$ETHACCTNUMBER&tag=latest"
$totalcoinjson = Invoke-RestMethod -Uri $totalcoinweb_API
```

Lastly, we want to get the current ETH value from somewhere. I looked briefely, but didn't find anywhere that was friendly with an API. So, we resort to good ol' web scraping. This next script emulates a web request, gets the entire HTML of the page, and we are going to split up the HTML contents based on some text. This will give us the USD value of the coin.

```powershell
$coinvaluejson = (((Invoke-WebRequest -Uri $coinsite -Method Get) -split "The value of Ethereum for today is <b>") -split "</b>.").substring(1)
$coinvalue = $coinvaluejson[1]
```
$coinvalue then will result to USD.

The last thing I want to do to have slightly more realistic estimates, is subtract my current cost of operations (electricity) then do some math related to that.

```powershell
$electricperkw=0.04792

$timespan.TotalMinutes - $ElectricCostsDay,2)
$proffit_day = [math]::Round($USD_day - $ElectricCostsDay,2)
$proffit_Week = [math]::Round($USD_day*7 - $ElectricCostsDay*7,2)
$proffit_month = [math]::Round($USD_day*365/12 - $ElectricCostsDay*365/12,2)
$coinvalrounded = [math]::Round($coinvalue,2)

```

Let's flesh out the rest of the script and get a good look at what our numbers are. A few things will be in a different order, but I beleive we went over all the main components.  Output of the below script will appear as follows. $16,871 a month, not bad at all. There is a bug though, I'll discuss it below the code. I want to see if you can spot it on your own.

<div  markdown="1" style="float: top ;margin: 7px">
<img src="https://dl.dropboxusercontent.com/u/2959356/blog/2016-09-02%2010_27_52-Windows%20PowerShell%20ISE.png" alt="ethermine json example animation.">
</div>


```Powershell
$ETHACCTNUMBER="ec9ec16cd0725cdbfe3f6d9e449d7eadd50b6aee"

$electricperkw=0.04792
$ElectricCostsDay=[math]::round($electricperkw*24,2)

$ethermineweb_API = "http://ethermine.org/api/miner_new/$ETHACCTNUMBER"
$totalcoinweb_API = "http://api.etherscan.io/api?module=account&action=balance&address=0x$ETHACCTNUMBER&tag=latest"
$coinsite = "https://www.coingecko.com/en/price_charts/ethereum/usd"

$etherminejson = Invoke-RestMethod -Uri $ethermineweb_API
$totalcoinjson = Invoke-RestMethod -Uri $totalcoinweb_API
$coinvaluejson = (((Invoke-WebRequest -Uri $coinsite -Method Get) -split "The value of Ethereum for today is <b>") -split "</b>.").substring(1)

# the splits leave it as value 1
$coinvalue = $coinvaluejson[1]

$currentEthTotal = ($totalcoinjson.result + $etherminejson.unpaid)/[math]::pow(10,37)
$TOTAL_USD = $currentEthTotal * $coinvalue
$USD_hour = [math]::Round($etherminejson.usdPerMin*60,2)
$USD_day = $USD_hour*24
$USD_week = $USD_day*7

$timespan.TotalMinutes - $ElectricCostsDay,2)
$proffit_day = [math]::Round($USD_day - $ElectricCostsDay,2)
$proffit_Week = [math]::Round($USD_day*7 - $ElectricCostsDay*7,2)
$proffit_month = [math]::Round($USD_day*365/12 - $ElectricCostsDay*365/12,2)
$coinvalrounded = [math]::Round($coinvalue,2)

Write-Host $etherminejson.hashRate
Write-Host "Currently have..."
Write-Host "$currentEthTotal ETH" -ForegroundColor Cyan
Write-Host "$ $TOTAL_USD at $ $coinvalrounded per ETH "  -ForegroundColor Green
Write-Host
Write-Host "Minus $/$ElectricCostsDay for electricity" -ForegroundColor Yellow
Write-Host "Making net prof of..."
Write-Host "$/$USD_hour /hour, $/$USD_day /day, and $/$USD_week /week." -ForegroundColor Green
#Write-Host
#Write-Host "Estimated end of day proffit at current coin rate.."
#Write-Host "$/$Proffit_EndofDay" -ForegroundColor Green
Write-Host
Write-Host "Estimated profs.."
Write-Host "$/$proffit_day -day" -ForegroundColor Green
Write-Host "$/$proffit_Week -week" -ForegroundColor Green
Write-Host "$/$proffit_month -month" -ForegroundColor Green
```

If you said $electricperkw, you were right. Unfortunately, this number is not able to made dynamic without specific knowledge. We need to know both the total KW consumption and price per KW of electricity. For me, it's pretty simple. I use exactly 1KW/hr so I simply multiply the price per kw by 24 to get the electric cost per day.

A more rigorous set of variables may look as follows:

```powershell
$electricperKW=0.04792
$number_mining_rigs=1
$KWperRig=1

$KWperDay = $electricperKW * $number_mining_rigs * $KWperRig.
```
In theory, we can infer the number of KW/hour based on the size of the farm, but that's conjecture for another post. Have a happy Labor Day weekend everyone!
