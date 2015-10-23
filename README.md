# App in the Air "Track Flight" Button

The main purpose of the repository is to allow booking app developers
integrate "Track Flight" button which will open installed "App in the Air" app
and add a flight to user's "My Flights" list.

*This is preview version of the API so please let us know any comments you have*

For now only iOS is supported (starting from App in the Air 5.1),
Android support would start from App in the Air 2.2.3 ([email us](mailto:support@appintheair.mobi) to get early access).

## Params ##
**Required Params**

| Param name             | Value          | Comment                                            |
| ---------------------- | -------------- | -------------------------------------------------- |
| `source`               | `mybookingapp` | url scheme of the initiating app                   |
| `flight[0].from`       | `JFK`          | origin airport code (prefers iata, but icao is ok) |
| `flight[0].to`         | `LAX`          | destination airport code                           |
| `flight[0].number`     | `137`          | flight number                                      |
| `flight[0].carrier`    | `AA`           | carrier code (prefers iata, but icao is ok)        |
| `flight[0].departure`  | `1445580900`   | LOCAL departure datetime                           |
| `flight[0].arrival  `  | `1445583600`   | LOCAL arrival datetime                             |
| `count`                | `1`            | number of flights in the current URL               |

**Optional Params (TBD)**

| Param name             | Value          | Comment                                               |
| ---------------------- | -------------- | ----------------------------------------------------- |
| `user`                 | `USERID`       | your user-id in order for cross-promo (contact us)    |
| `flight[0].bookRef`    | `FAK3BR`       | booking reference code                                |
| `flight[0].seat`       | `13A`          | seat number                                           |
| `flight[0].fare`       | `Y`            | fare that was used to book a flight (acc. to airline) |
| `flight[0].class`      | `Economy`      | class of the ticket                                   |
If you have more than 1 flight in one trip (multi-leg flight), than supply several flights as `flight[0] flight[1] etc`.

## iOS Implementation ##
A flight can be added using `appintheair://` URL Scheme.

Host URL will look like: `appintheair://trip`.

Don't forget to make it url-safe before passing to `canOpenURL:` or `openURL:`.

Example URL:
`appintheair://trip?source=aita&flight%5B0%5D.from=JFK&flight%5B0%5D.to=LAX&flight%5B0%5D.number=1377&flight%5B0%5D.carrier=AA&flight%5B0%5D.departure=1445580900&flight%5B0%5D.arrival=1445583600`

---
As of iOS9 you need to whitelist app you're going to open using `openURL:` under `LSApplicationQueriesSchemes` key in your `Info.plist` file.

If you don't have such key you can add it like this:
```
<key>LSApplicationQueriesSchemes</key>
<array>
<string>appintheair</string>
</array>
```

## Android Implementation ##

Params is the same, implementation and design guidelines are quite the same.

### Example usage ###
```
#!java

Uri.Builder builder = new Uri.Builder();
builder.scheme("appintheair");
builder.path("trip");
builder.appendQueryParameter("param_1", "value_1");
Intent intent = new Intent();
intent.setData(builder.build());
intent.setAction(Intent.ACTION_VIEW);
startActivity(intent);
```


## Note on Deeplinks ##
Before presenting "Track Flight" button in your UI you probably should check if App in the Air is installed on the current device using `UIApplication.sharedApplication().canOpenURL(url)` call for iOS. For Android the safest way would be to handle exception when you call 'startActivity(intent);'.

If App in the Air is not installed on the device, you can generate a url which will lead the user to the store and then to the newly installed app keeping parameters in mind (so the user will have a trip added after 'clean' install).

To do this you need to make
```
GET https://www.appintheair.mobi/api/prepare_deeplink?link=LINK
LINK -> your generated link without url-scheme (i.e. trip?source=XXX&...)
```
Example:
```
GET https://www.appintheair.mobi/api/prepare_deeplink?link=trip?source=test%26user=test%26flight%5B0%5D.from=SVO%26flight%5B0%5D.to=KJA%26flight%5B0%5D.number=1480%26flight%5B0%5D.carrier=SU%26flight%5B0%5D.departure=1448311500%26flight%5B0%5D.arrival=1448343000%26flight%5B0%5D.bookRef=FAK3BR%26flight%5B0%5D.seat=13A%26flight%5B0%5D.fare=Y%26flight%5B0%5D.class=Economy
```

In response you'll get JSON with `url` key. This url you should open when the user taps the button.

This behavior works with App in the Air iOS 5.1.3 and 2.2.3 Android.

## iOS Design Guidelines ##
You should place the button on the final screen of your booking flow, i.e. after the user's booked the flight.

Blue button if the preferred one, but you can also use white / black depending on your screen background.

> Custom design is also possible, but you should write us to approve it first.

Preferred size: 135x40 pts (270x80 px for @2x)

![](http://spronin.github.io/img/aita-track-en/aita_blue_en.png)

![](http://spronin.github.io/img/aita-track-en/aita_black_en.png)

![](http://spronin.github.io/img/aita-track-en/aita_white_en.png)

**[english .svg](http://spronin.github.io/svg/SVG_Outlined.zip)**

![](http://spronin.github.io/img/aita-track-ru/aita_blue_ru.png)

![](http://spronin.github.io/img/aita-track-ru/aita_black_ru.png)

![](http://spronin.github.io/img/aita-track-ru/aita_white_ru.png)

**[russian .svg](http://spronin.github.io/svg/SVG_RU_Outlined.zip)**

### Example usage ###
**Svyaznoy Travel**

![](http://spronin.github.io/img/aita-example/stravel.png)

**TDB example screen from andgo.travel**

## Android Design Guidelines ##
You should place the button on the final screen of your booking flow, i.e. after the user's booked the flight.
You must use our logo in your button. Our 'primary' color: #3295ba. Our 'accent' color: #1eaaf1. Those colors are also part of our brand, so you should use one of them.
In the next few weeks we would be able to share with you examples for flat and raised buttons respectively.



## Important note ##
Before submitting your app to the AppStore or Play Store you must send us your app description, screenshots of your interface with one of our buttons and your url-scheme (`source` param) to [support@appintheair.mobi](mailto:support@appintheair.mobi) in order to be whitelisted by our app.