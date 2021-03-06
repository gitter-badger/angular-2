<!--
{
"name" : "accessibility",
"version" : "0.1",
"title" : "Accessibility with ngAria",
"description" : "The goal of ngAria is to improve Angular's default accessibility by enabling common ARIA attributes that convey state or semantic information for assistive technologies used by persons with disabilities.",
"canonicalSource" : "https://docs.angularjs.org/guide/accessibility",
"homepage" : "https://docs.angularjs.org/guide",
"freshnessDate" : 2015-06-02,
"license" : "CC BY 3.0"
}
-->



<!-- @section -->

# Accessibility with ngAria

The goal of ngAria is to improve Angular's default accessibility by enabling common
[ARIA](http://www.w3.org/TR/wai-aria/) attributes that convey state or semantic information for
assistive technologies used by persons with disabilities.


<!-- @section -->

##Including ngAria

Using ngAria is as simple as requiring the ngAria module in your application. ngAria hooks into
standard AngularJS directives and quietly injects accessibility support into your application
at runtime.


```js
angular.module('myApp', ['ngAria'])...
```

###Using ngAria
Most of what ngAria does is only visible "under the hood". To see the module in action, once you've
added it as a dependency, you can test a few things:
 * Using your favorite element inspector, look for ngAria attributes in your own code.
 * Test using your keyboard to ensure `tabindex` is used correctly.
 * Fire up a screen reader such as VoiceOver to listen for ARIA support.
[Helpful screen reader tips.](http://webaim.org/articles/screenreader_testing/)


<!-- @section -->

##Supported directives
Currently, ngAria interfaces with the following directives:

 * ngModel
 * ngDisabled
 * ngShow
 * ngHide
 * ngClick
 * ngDblClick
 * ngMessages

<h2 id="ngmodel">ngModel</h2>

Most of ngAria's heavy lifting happens in the ngModel
directive.  For elements using ngModel, special attention is paid by ngAria if that element also
has a role or type of `checkbox`, `radio`, `range` or `textbox`.

For those elements using ngModel, ngAria will dynamically bind and update the following ARIA
attributes (if they have not been explicitly specified by the developer):

 * aria-checked
 * aria-valuemin
 * aria-valuemax
 * aria-valuenow
 * aria-invalid
 * aria-required

###Example

 
_Example file_: `index.html`

```javascript
 <style>
   [role=checkbox] {
     cursor: pointer;
     display: inline-block;
   }
   [role=checkbox] .icon:before {
     content: '\2610';
     display: inline-block;
     font-size: 2em;
     line-height: 1;
     vertical-align: middle;
     speak: none;
   }
   [role=checkbox].active .icon:before {
     content: '\2611';
   }
   pre {
     white-space: pre-wrap;
   }
 </style>
<div>
<form ng-controller="formsController">
 <some-checkbox role="checkbox" ng-model="checked" ng-class="{active: checked}"
   ng-disabled="isDisabled" ng-click="toggleCheckbox()"
   aria-label="Custom Checkbox" show-attrs>
   <span class="icon" aria-hidden="true"></span>
   Custom Checkbox
 </some-checkbox>
</form>
</div>
<script>
 var app = angular.module('ngAria_ngModelExample', ['ngAria'])
 .controller('formsController', function($scope){
   $scope.checked = false;
   $scope.toggleCheckbox = function(){
     $scope.checked = !$scope.checked;
   }
 })
 .directive('someCheckbox', function(){
   return {
     restrict: 'E',
     link: function($scope, $el, $attrs) {
       $el.on('keypress', function(event){
         event.preventDefault();
         if(event.keyCode === 32 || event.keyCode === 13){
           $scope.toggleCheckbox();
           $scope.$apply();
         }
       });
     }
   }
 })
 .directive('showAttrs', function() {
   return function($scope, $el, $attrs) {
     var pre = document.createElement('pre');
     $el.after(pre);
     $scope.$watch(function() {
       var $attrs = {};
       Array.prototype.slice.call($el[0].attributes, 0).forEach(function(item) {
         if (item.name !== 'show-$attrs') {
           $attrs[item.name] = item.value;
         }
       });
       return $attrs;
     }, function(newAttrs, oldAttrs) {
       pre.textContent = JSON.stringify(newAttrs, null, 2);
     }, true);
   }
 });
</script>
```


ngAria will also add `tabIndex`, ensuring custom elements with these roles will be reachable from
the keyboard. It is still up to **you** as a developer to **ensure custom controls will be
operable** from the keybard. Think of `ng-click` on a `<div>` or `<md-checkbox>`: you still need
to bind `ng-keypress` to make it fully operable from the keyboard. As a rule, any time you create
a widget involving user interaction, be sure to test it with your keyboard and at least one mobile
and desktop screen reader (preferably more).

<h2 id="ngdisabled">ngDisabled</h2>

The `disabled` attribute is only valid for certain elements such as `button`, `input` and
`textarea`. To properly disable custom element directives such as `<md-checkbox>` or `<taco-tab>`,
using ngAria with [ngDisabled](https://docs.angularjs.org/api/ng/directive/ngDisabled) will also
add `aria-disabled`. This tells assistive technologies when a non-native input is disabled, helping
custom controls to be more accessible.

###Example


```html
<md-checkbox ng-disabled="disabled">
```

Becomes:


```html
<md-checkbox disabled aria-disabled="true">
```

>You can check whether a control is legitimately disabled for a screen reader by visiting
[chrome://accessibility](chrome://accessibility).

<h2 id="ngshow">ngShow</h2>

>The [ngShow](https://docs.angularjs.org/api/ng/directive/ngShow) directive shows or hides the
given HTML element based on the expression provided to the `ngShow` attribute. The element is
shown or hidden by removing or adding the `.ng-hide` CSS class onto the element.

In its default setup, ngAria for `ngShow` is actually redundant. It toggles `aria-hidden` on the
directive when it is hidden or shown. However, the default CSS of `display: none !important`,
already hides child elements from a screen reader. It becomes more useful when the default
CSS is overridden with properties that don’t affect assistive technologies, such as `opacity`
or `transform`. By toggling `aria-hidden` dynamically with ngAria, we can ensure content visually
hidden with this technique will not be read aloud in a screen reader.

One caveat with this combination of CSS and `aria-hidden`: you must also remove links and other
interactive child elements from the tab order using `tabIndex=“-1”` on each control. This ensures
screen reader users won't accidentally focus on "mystery elements". Managing tab index on every
child control can be complex and affect performance, so it’s best to just stick with the default
`display: none` CSS. See the [fourth rule of ARIA use](http://www.w3.org/TR/aria-in-html/#fourth-rule-of-aria-use).

###Example
```css
.ng-hide {
  display: block;
  opacity: 0;
}
```

```html
<div ng-show="false" class="ng-hide" aria-hidden="true"></div>
```

Becomes:


```html
<div ng-show="true" aria-hidden="false"></div>
```
*Note: Child links, buttons or other interactive controls must also be removed from the tab order.*

<h2 id="nghide">ngHide</h2>

>The [ngHide](https://docs.angularjs.org/api/ng/directive/ngHide) directive shows or hides the
given HTML element based on the expression provided to the `ngHide` attribute. The element is
shown or hidden by removing or adding the `.ng-hide` CSS class onto the element.

The default CSS for `ngHide`, the inverse method to `ngShow`, makes ngAria redundant. It toggles
`aria-hidden` on the directive when it is hidden or shown, but the content is already hidden with
`display: none`. See explanation for ngShow when overriding the default CSS.

<h2><span id="ngclick">ngClick</span> and <span id="ngdblclick">ngDblclick</span></h2>
If `ng-click` or `ng-dblclick` is encountered, ngAria will add `tabindex="0"` if it isn't there
already.

To fix widespread accessibility problems with `ng-click` on div elements, ngAria will dynamically
bind keypress by default as long as the element isn't an anchor, button, input or textarea.
You can turn this functionality on or off with the `bindKeypress` configuration option. ngAria
will also add the `button` role to communicate to users of assistive technologies.

For `ng-dblclick`, you must still manually add `ng-keypress` and role to non-interactive elements such
as `div` or `taco-button` to enable keyboard access.

<h3>Example</h3>

```html
<div ng-click="toggleMenu()"></div>
```

Becomes:

```html
<div ng-click="toggleMenu()" tabindex="0"></div>
```

<h2 id="ngmessages">ngMessages</h2>

The new ngMessages module makes it easy to display form validation or other messages with priority
sequencing and animation. To expose these visual messages to screen readers,
ngAria injects `aria-live="assertive"`, causing them to be read aloud any time a message is shown,
regardless of the user's focus location.
###Example


```html
<div ng-messages="myForm.myName.$error">
  <div ng-message="required">You did not enter a field</div>
  <div ng-message="maxlength">Your field is too long</div>
</div>
```

Becomes:


```html
<div ng-messages="myForm.myName.$error" aria-live="assertive">
  <div ng-message="required">You did not enter a field</div>
  <div ng-message="maxlength">Your field is too long</div>
</div>
```


<!-- @section -->

##Disabling attributes
The attribute magic of ngAria may not work for every scenario. To disable individual attributes,
you can use the config method. Just keep in mind this will
tell ngAria to ignore the attribute globally.

 
_Example file_: `index.html`

```javascript
 <style>
   [role=checkbox] {
     cursor: pointer;
     display: inline-block;
   }
   [role=checkbox] .icon:before {
     content: '\2610';
     display: inline-block;
     font-size: 2em;
     line-height: 1;
     vertical-align: middle;
     speak: none;
   }
   [role=checkbox].active .icon:before {
     content: '\2611';
   }
 </style>
<form ng-controller="formsController">
 <div ng-model="someModel" show-attrs>
   Div with ngModel and aria-invalid disabled
 </div>
 <div role="checkbox" ng-model="checked" ng-class="{active: checked}"
   aria-label="Custom Checkbox" ng-click="toggleCheckbox()" some-checkbox show-attrs>
   <span class="icon" aria-hidden="true"></span>
   Custom Checkbox for comparison
 </div>
</form>
<script>
 angular.module('ngAria_ngDisabledExample', ['ngAria'], function config($ariaProvider) {
   $ariaProvider.config({
     ariaInvalid: false,
     tabindex: true
   });
 })
 .controller('formsController', function($scope){
   $scope.checked = false;
   $scope.toggleCheckbox = function(){
     $scope.checked = !$scope.checked;
   }
 })
 .directive('someCheckbox', function(){
   return {
     restrict: 'A',
     link: function($scope, $el, $attrs) {
       $el.on('keypress', function(event){
         event.preventDefault();
         if(event.keyCode === 32 || event.keyCode === 13){
           $scope.toggleCheckbox();
           $scope.$apply();
         }
       });
     }
   }
 })
 .directive('showAttrs', function() {
   return function(scope, el, attrs) {
     var pre = document.createElement('pre');
     el.after(pre);
     scope.$watch(function() {
       var attrs = {};
       Array.prototype.slice.call(el[0].attributes, 0).forEach(function(item) {
         if (item.name !== 'show-attrs') {
           attrs[item.name] = item.value;
         }
       });
       return attrs;
     }, function(newAttrs, oldAttrs) {
       pre.textContent = JSON.stringify(newAttrs, null, 2);
     }, true);
   }
 });
</script>
```



<!-- @section -->

##Common Accessibility Patterns

Accessibility best practices that apply to web apps in general also apply to Angular.

 * **Text alternatives**: Add alternate text content to make visual information accessible using
 [these W3C guidelines](http://www.w3.org/TR/html-alt-techniques/). The appropriate technique
 depends on the specific markup but can be accomplished using offscreen spans, `aria-label` or
 label elements, image `alt` attributes, `figure`/`figcaption` elements and more.
 * **HTML Semantics**: If you're creating custom element directives, Web Components or HTML in
 general, use native elements wherever possible to utilize built-in events and properties.
 Alternatively, use ARIA to communicate semantic meaning. See [notes on ARIA use](http://www.w3.org/TR/aria-in-html/#notes-on-aria-use-in-html).
 * **Focus management**: Guide the user around the app as views are appended/removed.
 Focus should *never* be lost, as this causes unexpected behavior and much confusion (referred to
 as "freak-out mode").
 * **Announcing changes**: When filtering or other UI messaging happens away from the user's focus,
 notify with [ARIA Live Regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions).
 * **Color contrast and scale**: Make sure content is legible and interactive controls are usable
 at all screen sizes. Consider configurable UI themes for people with color blindness, low vision
 or other visual impairments.
 * **Progressive enhancement**: Some users do not browse with JavaScript enabled or do not have
 the latest browser. An accessible message about site requirements can inform users and improve
 the experience.


<!-- @section -->

## Additional Resources

 * [Using ARIA in HTML](http://www.w3.org/TR/aria-in-html/)
 * [AngularJS Accessibility at ngEurope](https://www.youtube.com/watch?v=dmYDggEgU-s&list=UUEGUP3TJJfMsEM_1y8iviSQ)
 * [Testing with Screen Readers](http://webaim.org/articles/screenreader_testing/)
 * [Chrome Accessibility Developer Tools](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en)
 * [W3C Accessibility Testing](http://www.w3.org/wiki/Accessibility_testing)
 * [WebAIM](http://webaim.org)
 * [A11y Project](http://a11yproject.com)
