# ionic-letter-avatar-selector  

This plugin provides letter avatars for list items and multiple list item selection similar to android gmail app. 
It can be integrated in your ionic 1.x app using the `ion-item` directive.

## Short demo  
![animated example](http://i.giphy.com/3o6gbcO6uExMGe2xuE.gif)

## Features 
* letter avatars for ionic list items based on the `ionItem` directive
* multiple selection and deselection of list items by clicking on a letter avatar or long clicking (1s) on a list item
* retrieval of selected items
* two buttons for finishing a selection and deleting selected items


## Usage 
Get the files from github or install from bower:
```
bower install ionic-letter-avatar-selector
```

```html
<link href="../ionic-letter-avatar-selector.min.css" rel="stylesheet">
<script src="../ionic-letter-avatar-selector.min.js"></script>
```

Add the module `ionicLetterAvatarSelector` to your application dependencies:

```javascript
angular.module('starter', ['ionic', 'ionicLetterAvatarSelector'])
```

Done!

## Configuration provider

In the angular configuration phase you can define global settings for this plugin. For that purpose there is a provider named `$ionicLetterAvatarSelectorConfigProvider`.
The following options can be set in the configuration phase:

option|description|type|accepted values|default value
---|---|---|---|---|---
background|Background color of letter avatars|String|hex color codes, ionic color names (e.g. 'positive') and 'random' for random colors|positive
color|Foreground color of letter avatars (text color)|String|hex color codes, css color names and ionic color names|light
border|Border of letter avatars|String|css code|none
number of letters|Number of letters to show inside a letter avatar|Number|1-n|the default letter number is detected depending on word number in a given string
selection on Android|Enabling / disabling selection on Android|Boolean|true, false|true
selection on iOS|Enabling / disabling selection on iOS|Boolean|true, false|false
selection color|Color of avatars and navigation bar during selection|String|hex color codes|#A8A8A8

#### Example
##### Code

```javascript
angular.module('starter', ['ionic', 'ionicLetterAvatarSelector'])
.config(function($ionicLetterAvatarSelectorConfigProvider) {
    $ionicLetterAvatarSelectorConfigProvider.setBackground("calm");
    $ionicLetterAvatarSelectorConfigProvider.setColor("light");
    $ionicLetterAvatarSelectorConfigProvider.setBorder("1px solid black");
    $ionicLetterAvatarSelectorConfigProvider.setLetters(3);
    $ionicLetterAvatarSelectorConfigProvider.setSelectionAndroid(false);
    $ionicLetterAvatarSelectorConfigProvider.setSelectionIos(true);
    $ionicLetterAvatarSelectorConfigProvider.setSelectionColor("#86b0f9");
});
```

##### Result
![screenshot1](http://fs5.directupload.net/images/160223/r766ommj.png)

## Services

### Service `$ionicLetterAvatarSelector`

Using this service you have access to the following events and functions:

event|description|return-value
---|---|---|---
`started`|Selection started event|
`finished`|Selection finished event|
`stateChanged`|Selection state event|true = selection active / false = selection inactive

function|description
---|---|---
`getData()`|Retrieval of selected items as array
`finish()`|Finishing selection

## Directives
### Extended directive `ion-item`

Add the following mandatory attributes to the `ion-item` directive of your list items:

attribute|description
---|---|---
`letters-of`|String value from which the first letter(s) will be used for building a letter avatar
`item`|Corresponding list item identifier which will be cached during selection

The following attributes are optional and can be also set globally in the configuration phase via configuration provider:

attribute|description|accepted values|default value
---|---|---|---|---
`background`|Background color of letter avatars|hex color codes, ionic color names (e.g. 'positive') and 'random' for random colors|positive
`color`|Foreground color of letter avatars (text color)|hex color codes and ionic color names|light
`border`|Border of letter avatars|css code|none
`letters`|Number of letters to show|1-n|the default letter number is detected depending on word number in a given string

#### Example
##### Code

```html
<ion-list>
    <ion-item letters-of="{{chat.name}}"
              item="{{chat.id}}"
              background="random"
              letters="1"
              href="#/tab/chats/{{chat.id}}"
              ng-repeat="chat in chats">
        <h2>{{chat.name}}</h2>
        <p>{{chat.lastText}}</p>
        <i class="icon ion-chevron-right icon-accessory"></i>
    </ion-item>
</ion-list>
```
##### Result

![screenshot2](http://fs5.directupload.net/images/160222/z72w4mqz.png)

### Directive `ionic-letter-avatar-selector-button-finish`

You can use this directive to show / hide a button for finishing item selection. The default icons for iOS and Android are `ion-checkmark-empty` and `ion-android-arrow-back`.

#### Example

```html
<ion-nav-bar class="bar-positive">
    <ion-nav-back-button></ion-nav-back-button>
    <ion-nav-buttons side="left">
        <ionic-letter-avatar-selector-button-finish>
        </ionic-letter-avatar-selector-button-finish>
    </ion-nav-buttons>    
</ion-nav-bar>
```

### Directive `ionic-letter-avatar-selector-button-delete`

This directive shows / hides a button for deleting selected items. The default icons for iOS and Android are `ion-ios-trash` and `ion-android-delete`.
Using `ng-click` attribute you can invoke your specific delete function considering the following actions: 

1. In your delete function first get selected item identifiers invoking the `getData()` function of the `$ionicLetterAvatarSelector` service.
2. Write your code for deleting selected items. 
3. Finish selection invoking the `finish()` function of the above-mentioned service.

#### Example

```html
<ion-view view-title="Chats">
    <ion-nav-buttons side="right">
        <ionic-letter-avatar-selector-button-delete ng-click="delete()">
        </ionic-letter-avatar-selector-button-delete>
    </ion-nav-buttons>  
</ion-view>
```

```javascript
angular.module('starter.controllers', [])
.controller('ChatsCtrl', function($ionicLetterAvatarSelector, $scope, Chats) {
    $scope.delete = function() {
        var chats = Chats.all();
        
        //1. Getting selected items
        var selectedIDs = $ionicLetterAvatarSelector.getData();
        
        //2. Deleting items
        selectedIDs.forEach(function(id) {
            var chat = chats.filter(function(chat) {
                return chat.id === id;
            })[0];
            chats.splice(chats.indexOf(chat), 1);
        });
        
        //3. Finishing selection
        $ionicLetterAvatarSelector.finish();
    };
})
```

Instead of using this directives you can also add your own buttons and show / hide them depending on selection state event.

#### Example
##### Code

```html
<ion-view view-title="Chats">
    <ion-nav-buttons side="left">
        <button class="button button-icon icon ion-checkmark-circled" 
                ng-show="selectionActive" 
                ng-click="finish()">
        </button>
    </ion-nav-buttons>  
    <ion-nav-buttons side="right">
        <button class="button button-icon icon ion-trash-b" 
                ng-show="selectionActive" 
                ng-click="delete()">
        </button>
    </ion-nav-buttons>  
    <ion-content>
        <ion-list>
            <ion-item letters-of="{{chat.name}}"
                      item="{{chat.id}}"
                      href="#/tab/chats/{{chat.id}}"
                      ng-repeat="chat in chats" >
                <h2>{{chat.name}}</h2>
                <p>{{chat.lastText}}</p>
                <i class="icon ion-chevron-right icon-accessory"></i>
            </ion-item>
        </ion-list>
    </ion-content>
</ion-view>
```

```javascript
angular.module('starter.controllers', [])
.controller('ChatsCtrl',function($ionicLetterAvatarSelector, $scope, Chats) {
    $scope.$on($ionicLetterAvatarSelector.stateChanged, function(state) {
        $scope.selectionActive = state;
    });
    
    $scope.finish = $ionicLetterAvatarSelector.finish;
    
    $scope.delete = function() {
        var chats = Chats.all();
        var selectedIDs = $ionicLetterAvatarSelector.getData();
        selectedIDs.forEach(function(id) {
            var chat = chats.filter(function(chat) {
                return chat.id === id;
            })[0];
            chats.splice(chats.indexOf(chat), 1);
        });
        $scope.finish();
    };
});
```

##### Result

![screenshot3](http://fs5.directupload.net/images/160222/m5heu6xg.png)

## Contact

* gmail : i.weber1983@gmail.com
* twitter : https://twitter.com/_ivanweber_
* paypal : ivan.weber@gmx.de
