# Web Chat API as React hooks

Web Chat is designed to be highly customizable. In order to build your own UI, you can use React Hooks to hook your UI component into Web Chat API.

To enable Web Chat API, all UI components must be located under the [`<Composer>`](https://github.com/microsoft/BotFramework-WebChat/blob/master/packages/component/src/Composer.js) component. You can refer to our [plain UI customization](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/06.recomposing-ui/d.plain-ui) sample for details.

## Why React Hooks

React Hooks will make your code cleaner and shorter, and also greatly improve readability.

Web Chat exposes APIs through React Hooks. This API surface enables us to freely move things behind the scene, introduce new APIs, and safely deprecate APIs. It will also make it easier to shuffle work between the internal Redux store and React Context.

## Two types of hooks

We design our hooks largely with two basic shapes:

-  Actions, these are functions that you can call at any time to perform a side-effect
-  Properties, these are getter functions with an optional setter
   -  This is same as [React State Hook pattern](https://reactjs.org/docs/hooks-state.html), but setters are optional
   -  If the value is changed, React will call your render function again

### Actions

All actions will return a function that can be called at a later point. For example, if you want to focus to the send box, you will use the following code:

<!-- prettier-ignore-start -->
```js
const focusSendBox = useFocusSendBox();

focusSendBox();
```
<!-- prettier-ignore-end -->

### Properties

All properties follow [React State Hook pattern](https://reactjs.org/docs/hooks-state.html). For example, if you want to get and set the value of send box, you will use the following code:

<!-- prettier-ignore-start -->
```js
const [sendBoxValue, setSendBoxValue] = useSendBoxValue();

console.log(`The send box value is "${sendBoxValue}".`);

setSendBoxValue('Hello, World!');
```
<!-- prettier-ignore-end -->

> Note: some properties may not be settable.

# List of hooks

Following is the list of hooks supported by Web Chat API.

-  [`useActiveTyping`](#useactivetyping)
-  [`useActivities`](#useactivities)
-  [`useAdaptiveCardsHostConfig`](#useadaptivecardshostconfig)
-  [`useAdaptiveCardsPackage`](#useadaptivecardspackage)
-  [`useAvatarForBot`](#useavatarforbot)
-  [`useAvatarForUser`](#useavatarforuser)
-  [`useByteFormatter`](#useByteFormatter)
-  [`useConnectivityStatus`](#useconnectivitystatus)
-  [`useDateFormatter`](#useDateFormatter)
-  [`useDebouncedNotification`](#usedebouncednotification)
-  [`useDictateInterims`](#usedictateinterims)
-  [`useDictateState`](#usedictatestate)
-  [`useDirection`](#useDirection)
-  [`useDisabled`](#usedisabled)
-  [`useDismissNotification`](#usedismissnotification)
-  [`useEmitTypingIndicator`](#useemittypingindicator)
-  [`useFocusSendBox`](#usefocussendbox)
-  [`useGrammars`](#usegrammars)
-  [`useGroupTimestamp`](#usegrouptimestamp)
-  [`useLanguage`](#uselanguage)
-  [`useLastTypingAt`](#uselasttypingat)
-  [`useLocalize`](#uselocalize) (Deprecated)
-  [`useLocalizer`](#useLocalizer)
-  [`useLastTypingAt`](#uselasttypingat) (Deprecated)
-  [`useMarkActivityAsSpoken`](#usemarkactivityasspoken)
-  [`useNotification`](#usenotification)
-  [`usePerformCardAction`](#useperformcardaction)
-  [`usePostActivity`](#usepostactivity)
-  [`useReferenceGrammarID`](#usereferencegrammarid)
-  [`useRelativeTimeFormatter`](#useRelativeTimeFormatter)
-  [`useRenderActivity`](#userenderactivity)
-  [`useRenderActivityStatus`](#userenderactivitystatus)
-  [`useRenderAttachment`](#userenderattachment)
-  [`useRenderMarkdownAsHTML`](#userendermarkdownashtml)
-  [`useRenderToast`](#userendertoast)
-  [`useRenderTypingIndicator`](#userendertypingindicator)
-  [`useScrollToEnd`](#usescrolltoend)
-  [`useSendBoxValue`](#usesendboxvalue)
-  [`useSendEvent`](#usesendevent)
-  [`useSendFiles`](#usesendfiles)
-  [`useSendMessage`](#usesendmessage)
-  [`useSendMessageBack`](#usesendmessageback)
-  [`useSendPostBack`](#usesendpostback)
-  [`useSendTimeoutForActivity`](#usesendtimeoutforactivity)
-  [`useSendTypingIndicator`](#usesendtypingindicator)
-  [`useSetNotification`](#usesetnotification)
-  [`useShouldSpeakIncomingActivity`](#useshouldspeakincomingactivity)
-  [`useStartDictate`](#usestartdictate)
-  [`useStopDictate`](#usestopdictate)
-  [`useStyleOptions`](#usestyleoptions)
-  [`useStyleSet`](#usestyleset)
-  [`useSubmitSendBox`](#usesubmitsendbox)
-  [`useSuggestedActions`](#usesuggestedactions)
-  [`useTimeoutForSend`](#usetimeoutforsend)
-  [`useTrackDimension`](#usetrackdimension)
-  [`useTrackEvent`](#usetrackevent)
-  [`useTrackException`](#usetrackexception)
-  [`useTrackTiming`](#usetracktiming)
-  [`useUserID`](#useuserid)
-  [`useUsername`](#useusername)
-  [`useVoiceSelector`](#usevoiceselector)
-  [`useWebSpeechPonyfill`](#usewebspeechponyfill)

## `useActiveTyping`

<!-- prettier-ignore-start -->
```js
interface Typing {
  at: number;
  expireAt: number;
  name: string;
  role: 'bot' | 'user';
}

useActiveTyping(expireAfter?: number): [{ [id: string]: Typing }]
```
<!-- prettier-ignore-end -->

This function will return a list of participants who are actively typing, including the start typing time (`at`) and expiration time (`expireAt`), the name and the role of the participant.

If the participant sends a message after the typing activity, the participant will be explicitly removed from the list. If no messages or typing activities are received, the participant is considered inactive and not listed in the result. To keep the typing indicator active, participants should continuously send the typing activity.

The `expireAfter` argument can override the inactivity timer. If `expireAfter` is `Infinity`, it will return all participants who did not explicitly remove from the list. In other words, it will return participants who sent a typing activity, but did not send a message activity afterward.

> This hook will trigger render of your component if one or more typing information is expired or removed.

## `useActivities`

<!-- prettier-ignore-start -->
```js
useActivities(): [Activity[]]
```
<!-- prettier-ignore-end -->

This function will return a list of activities.

## `useAdaptiveCardsHostConfig`

<!-- prettier-ignore-start -->
```js
useAdaptiveCardsHostConfig(): [AdaptiveCards.HostConfig]
```
<!-- prettier-ignore-end -->

This function is only available in full bundle. The function will return the Adaptive Cards Host Config used for styling Adaptive Cards.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useAdaptiveCardsPackage`

<!-- prettier-ignore-start -->
```js
useAdaptiveCardsPackage(): [AdaptiveCards]
```
<!-- prettier-ignore-end -->

This function is only available in full bundle. The function will return the Adaptive Cards package used for building and rendering Adaptive Cards.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useAvatarForBot`

<!-- prettier-ignore-start -->
```js
useAvatarForBot(): [{
  image: string,
  initials: string
}]
```
<!-- prettier-ignore-end -->

This function will return the image and initials of the bot. Both image and initials are optional and can be falsy.

To set the avatar for the bot, change the props passed to Web Chat via style options.

## `useAvatarForUser`

<!-- prettier-ignore-start -->
```js
useAvatarForUser(): [{
  image: string,
  initials: string
}]
```
<!-- prettier-ignore-end -->

This function will return the image and initials of the user. Both image and initials are optional and can be falsy.

To set the avatar for the user, change the props passed to Web Chat via style options.

## `useByteFormatter`

> New in 4.9.0.

<!-- prettier-ignore-start -->
```js
useByteFormatter() => (bytes: number) => string
```
<!-- prettier-ignore-end -->

This function will return a function that, when called with a file size, will return a localized representation of the size in bytes, kilobytes, megabytes, or gigabytes. It honors the language settings from the `useLanguage` hook.

## `useConnectivityStatus`

<!-- prettier-ignore-start -->
```js
useConnectivityStatus(): [string]
```
<!-- prettier-ignore-end -->

This function will return the Direct Line connectivity status:

-  `connected`: Connected
-  `connectingslow`: Connecting is incomplete and more than 15 seconds have passed
-  `error`: Connection error
-  `notconnected`: Not connected, related to invalid credentials
-  `reconnected`: Reconnected after interruption
-  `reconnecting`: Reconnecting after interruption
-  `sagaerror`: Errors on JavaScript renderer; please see the browser's console
-  `uninitialized`: Initial connectivity state; never connected and not attempting to connect.

## `useDateFormatter`

> New in 4.9.0.

<!-- prettier-ignore-start -->
```js
useDateFormatter() => (dateOrString: (Date | number | string)) => string
```
<!-- prettier-ignore-end -->

This function will return a function that, when called with a `Date` object, `number`, or `string`, will return a localized representation of the date in absolute time. It honors the language settings from the `useLanguage` hook.

## `useDebouncedNotification`

<!-- prettier-ignore-start -->
```js
interface Notification {
  alt?: string;
  id: string;
  level: 'error' | 'info' | 'success' | 'warn' | string;
  message: string;
}

useDebouncedNotifications(): [Notification[]]
```
<!-- prettier-ignore-end -->

When called, this hook will return a debounced array of notifications.

Due to debouncing, notifications retrieved using this hook may not be current. At the time of convergence, this hook will trigger another render.

For the debounce behavior, please read our [article regarding notification system](https://github.com/microsoft/BotFramework-WebChat/tree/master/docs/NOTIFICATION.md).

## `useDictateInterims`

<!-- prettier-ignore-start -->
```js
useDictateInterims(): [string[][]]
```
<!-- prettier-ignore-end -->

This function will return active interims processed from a dictation event.

The first array represents separate sentences while the second array represents potential ambiguities or alternatives for the same sentence.

## `useDictateState`

<!-- prettier-ignore-start -->
```js
useDictateState(): [string]
```
<!-- prettier-ignore-end -->

This function will return one of the following dictation states:

-  `IDLE`: Recognition engine is idle; not recognizing
-  `WILL_START`: Will start recognition after synthesis completed
-  `STARTING`: Recognition engine is starting; not accepting any inputs
-  `DICTATING`: Recognition engine is accepting input
-  `STOPPING`: Recognition engine is stopping; not accepting any inputs

> Please refer to `Constants.DictateState` in `botframework-webchat-core` for up-to-date details.

To control dictate state, use the [`useStartDictate`](#usestartdictate) and [`useStopDictate`](#usestopdictate) hooks.

## `useDirection`

<!-- prettier-ignore-start -->
```js
useDirection(): [string]
```
<!-- prettier-ignore-end -->

This function will return one of two language directions:

-  `ltr` or otherwise: Web Chat UI will display as left-to-right
-  `rtl`: Web Chat UI will display as right-to-left

This value will be automatically configured based on the `locale` of Web Chat.

If you would prefer to set this property manually, change the value `dir` prop passed to Web Chat.

## `useDisabled`

<!-- prettier-ignore-start -->
```js
useDisabled(): [boolean]
```
<!-- prettier-ignore-end -->

This function will return whether the UI should be disabled or not. All interactable UI components should honor this value.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useDismissNotification`

<!-- prettier-ignore-start -->
```js
useDismissNotification(): (id: string) => void
```
<!-- prettier-ignore-end -->

This hook will return a function which can be called to dismiss a notification given its ID.

## `useEmitTypingIndicator`

<!-- prettier-ignore-start -->
```js
useEmitTypingIndicator(): () => void
```
<!-- prettier-ignore-end -->

When called, this function will send a typing activity from the user to the bot.

## `useFocusSendBox`

<!-- prettier-ignore-start -->
```js
useFocusSendBox(): () => void
```
<!-- prettier-ignore-end -->

When called, this function will send focus to the send box.

## `useGrammars`

<!-- prettier-ignore-start -->
```js
useGrammars(): [string[]]
```
<!-- prettier-ignore-end -->

This function will return grammars for speech-to-text. Grammars is a list of words provided by the implementer for the speech-to-text engine to bias towards. It is commonly used for selecting the correct words with same or similar pronunciations, e.g. Bellevue vs. Bellview vs. Bellvue.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useGroupTimestamp`

<!-- prettier-ignore-start -->
```js
useGroupTimestamp(): [number]
```
<!-- prettier-ignore-end -->

This function will return the interval for grouping similar activities with a single timestamp. The interval is represented in milliseconds.

For example, if this value is `5000`, successive activities within 5 seconds will share the timestamp of the first message.

To control the `groupTimestamp` state, change the props passed to Web Chat via style options.

## `useLanguage`

<!-- prettier-ignore-start -->
```ts
type LanguageOptions = 'speech';

useLanguage(options?: LanguageOptions): [string]
```
<!-- prettier-ignore-end -->

This function will return the language of the UI. All UI components should honor this value.

If no options are passed, the return value will be the written language. This value should be the same as `props.locale` passed to `<ReactWebChat>` or `<Composer>`.

If `"speech"` is passed to `options`, the return value will be the oral language instead of written language. For example, the written language for Hong Kong and Taiwan are Traditional Chinese, while the oral language are Cantonese and Taiwanese Mandarin respectively.

To modify this value, change the value in the `locale` prop passed to Web Chat.

## `useLastTypingAt`

<!-- prettier-ignore-start -->
```js
useLastTypingAt(): [{
  [id: string]: number
}]
```
<!-- prettier-ignore-end -->

This function will return a map of the last typing time of all participants. The time is based on the client clock.

This property is computed on every incoming activity.

## `useLocalize`

<!-- prettier-ignore-start -->
```js
useLocalize(identifier: string) => string
```
<!-- prettier-ignore-end -->

> This function is deprecated. Developers should migrate to [`useLocalizer`](#useLocalizer).

This function will return a localized string represented by the identifier. It honors the language settings from the `useLanguage` hook.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useLocalizer`

> New in 4.9.0.

<!-- prettier-ignore-start -->
```js
interface LocalizerOptions {
  plural: Boolean;
}

useLocalizer(options: LocalizerOptions) => (identifier: string, ...arguments: string[]) => string
```
<!-- prettier-ignore-end -->

This function, when called, will return a localized string represented by the identifier and its arguments. It honors the language settings from the `useLanguage` hook.

### Plural form

<!-- prettier-ignore-start -->
```js
interface PluralRulesIdentifier {
  zero?: string,
  one?: string,
  two?: string,
  few?: string,
  many?: string,
  other: string
}

useLocalizer({ plural: true }) => (identifiers: PluralRulesIdentifier, firstArgument: number, ...otherArguments: string[]) => string
```
<!-- prettier-ignore-end -->

Some localized strings may contains words that have multiple plural forms, for example, "1 notification" and "2 notifications".

Web Chat supports multiple plural forms based on [Unicode Plural Rules](http://cldr.unicode.org/index/cldr-spec/plural-rules). For a single string, you will need to specify up to 6 strings (zero, one, two, few, many, other). You must at least specify string that represent the "other" plural rule.

The first argument must be a number and used to select which plural form to use. Only cardinal is supported at this time.

<!-- prettier-ignore-start -->
```js
const localize = useLocalize({ plural: true });

// The following code will print "2 notifications" to console.

console.log(
   localize(
      {
         zero: 'No notifications',
         one: 'A notification',
         two: '$1 notifications',
         few: '$1 notifications',
         many: '$1 notifications',
         other: '$1 notifications'
      },
      2
   )
);
```
<!-- prettier-ignore-end -->

## `useMarkActivityAsSpoken`

<!-- prettier-ignore-start -->
```js
useMarkActivityAsSpoken(): (activity: Activity) => void
```
<!-- prettier-ignore-end -->

When called, this function will mark the activity as spoken and remove it from the text-to-speech queue.

## `useNotifications`

<!-- prettier-ignore-start -->
```js
interface Notification {
  alt?: string;
  id: string;
  level: 'error' | 'info' | 'success' | 'warn' | string;
  message: string;
}

useNotifications(): [Notification[]]
```
<!-- prettier-ignore-end -->

When called, this hook will return an array of notifications.

## `usePerformCardAction`

<!-- prettier-ignore-start -->
```js
usePerformCardAction(): ({
  displayText: string,
  text: string,
  type: string,
  value: string
}) => void
```
<!-- prettier-ignore-end -->

When called, this function will perform the card action based on its `type`. The card action will be performed by `cardActionMiddleware`.

List of supported card action types can be found in this [Direct Line Activity card action schema](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#type-1).

## `usePostActivity`

<!-- prettier-ignore-start -->
```js
usePostActivity(): (activity: Activity) => void
```
<!-- prettier-ignore-end -->

When called, this function will post the activity on behalf of the user, to the bot.

You can use this function to send any type of activity to the bot, however we highly recommend limiting the [activity types](https://github.com/microsoft/BotFramework-WebChat/tree/master/docs/ACTIVITYTYPES.md) to one of the following:

-  `event`
-  `message`
-  `typing`

## `useReferenceGrammarID`

<!-- prettier-ignore-start -->
```js
useReferenceGrammarId(): [string]
```
<!-- prettier-ignore-end -->

When called, this function will return the reference grammar ID used to improve speech-to-text performance when used with Cognitive Services.

This value is not controllable and is passed to Web Chat from the Direct Line channel.

## `useRelativeTimeFormatter`

> New in 4.9.0.

<!-- prettier-ignore-start -->
```js
useRelativeTimeFormatter() => (dateOrString: (Date | number | string)) => string
```
<!-- prettier-ignore-end -->

This function will return a function that, when called with a `Date` object, `number`, or `string`, will return a localized representation of the date in relative time, e.g. "2 minutes ago". It honors the language settings from the `useLanguage` hook.

## `useRenderActivity`

<!-- prettier-ignore-start -->
```js
useRenderActivity(
  renderAttachment: ({
    activity: Activity,
    attachment: Attachment
  }) => React.Element
): ({
  activity: Activity,
  nextVisibleActivity: Activity
}) => React.Element
```
<!-- prettier-ignore-end -->

This function is for rendering an activity and its attachments inside a React element. Because of the parent-child relationship, the caller will need to pass a render function in order for the attachment to create a render function for the activity. When rendering the activity, the caller will need to pass `activity` and `nextVisibleActivity`. This function is a composition of `activityRendererMiddleware`, which is passed as a prop.

Note that not all activities are rendered, e.g. the event activity. Because of this, those activities will not be rendered. The `nextVisibleActivity` is the pointer to the next visible activity and is intended for the activity status renderer on grouping timestamps for adjacent activities.

### New in 4.8.0

Previously, we use `timestampClassName` to control if the activity should show timestamp or not. The `timestampClassName` should be add as a `class` attribute the DOM element which contains the timestamp.

Today, we pass `activity` and `nextVisibleActivity` to the middleware, so the `activityRendererMiddleware` make the decision about timestamp visibility. For example, developers can group timestamp based on activity type.

## `useRenderActivityStatus`

<!-- prettier-ignore-start -->
```js
useRenderActivityStatus(): ({
  activity: Activity,
  nextVisibleActivity: Activity
}) => React.Element
```
<!-- prettier-ignore-end -->

This function is for rendering the status of an activity. The caller will need to pass `activity` and `nextVisibleActivity` as parameters. This function is a composition of `activityStatusRendererMiddleware`, which is passed as a prop.

## `useRenderAttachment`

<!-- prettier-ignore-start -->
```js
useRenderAttachment(): ({
  activity: Activity,
  attachment: Attachment
}) => React.Element
```
<!-- prettier-ignore-end -->

This function is for rendering an attachments inside a React element. The caller will need to pass `activity` and `attachment` as parameters. This function is a composition of `attachmentRendererMiddleware`, which is passed as a prop.

<!-- prettier-ignore-start -->
```js
() => next => { activity, attachment } => next({ activity, attachment })
```
<!-- prettier-ignore-end -->

## `useRenderMarkdownAsHTML`

<!-- prettier-ignore-start -->
```js
useRenderMarkdownAsHTML(): (markdown: string): string
```
<!-- prettier-ignore-end -->

This function will return a function that, when called, will render Markdown into an HTML string. For example,

<!-- prettier-ignore-start -->
```js
const renderMarkdown = useRenderMarkdown();

renderMarkdown('Hello, World!') === '<p>Hello, World!</p>\n';
```
<!-- prettier-ignore-end -->

The Markdown engine can be reconfigured by passing `renderMarkdown` prop to Web Chat. The default engine is a customized [Markdown-It](https://npmjs.com/package/markdown-it) with [HTML sanitizer](https://npmjs.com/package/sanitize-html) and [support `aria-label` attribute](https://npmjs.com/package/markdown-it-attrs). The customization can be found in [bundle/src/renderMarkdown.js](https://github.com/microsoft/BotFramework-WebChat/tree/master/packages/bundle/src/renderMarkdown.js).

## `useRenderToast`

<!-- prettier-ignore-start -->
```js
interface Notification {
  alt?: string;
  id: string;
  level: 'error' | 'info' | 'success' | 'warn' | string;
  message: string;
}

useRenderToast(): ({ notification: Notification }) => React.Element
```
<!-- prettier-ignore-end -->

This function is for rendering a toast for the notification toaster. The caller will need to pass `notification` as parameter to the function. This function is a composition of `toastMiddleware`, which is passed as a prop to Web Chat.

## `useRenderTypingIndicator`

<!-- prettier-ignore-start -->
```js
interface Typing {
  at: number;
  expireAt: number;
  name: string;
  role: 'bot' | 'user';
}

useRenderTypingIndicator():
  ({
    activeTyping: { [id: string]: Typing },
    typing: { [id: string]: Typing },
    visible: boolean
  }) => React.Element
```
<!-- prettier-ignore-end -->

This function is for rendering typing indicator for all participants of the conversation. This function is a composition of `typingIndicatorMiddleware`, which is passed as a prop to Web Chat. The caller will pass the following arguments:

-  `activeTyping` lists of participants who are actively typing.
-  `typing` lists participants who did not explicitly stopped typing. This list is a superset of `activeTyping`.
-  `visible` indicates whether typing indicator should be shown in normal case. This is based on participants in `activeTyping` and their `role` (role not equal to `"user"`).

## `useScrollToEnd`

<!-- prettier-ignore-start -->
```js
useScrollToEnd(): () => void
```
<!-- prettier-ignore-end -->

This function will return a function that, when called, will scroll the transcript view to the end.

## `useSendBoxValue`

<!-- prettier-ignore-start -->
```js
useSendBoxValue(): [string, (value: string) => void]
```
<!-- prettier-ignore-end -->

This function will return the current value of the send box and the setter function to change the value.

## `useSendEvent`

<!-- prettier-ignore-start -->
```js
useSendEvent(): (name: string, value: string) => void
```
<!-- prettier-ignore-end -->

When called, this function will send an event activity to the bot.

## `useSendFiles`

<!-- prettier-ignore-start -->
```js
useSendFiles(): (files: (Blob | File)[]) => void
```
<!-- prettier-ignore-end -->

When called, this function will send a message activity with one or more [File](https://developer.mozilla.org/en-US/docs/Web/API/File) attachments to the bot, including these operations:

-  Convert [File](https://developer.mozilla.org/en-US/docs/Web/API/File) into object URL
-  Generate thumbnail and will use a Web Worker and an offscreen canvas if supported

If you are using an `ArrayBuffer`, you can use `FileReader` to convert it into a blob before calling [`URL.createObjectURL`](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL).

## `useSendMessage`

<!-- prettier-ignore-start -->
```js
useSendMessage(): (text: string, method: string) => void
```
<!-- prettier-ignore-end -->

When called, this function will send a text message activity to the bot.

You can optionally include the input method how the text message was collected. Currently, if specified, only `speech` is supported.

## `useSendMessageBack`

<!-- prettier-ignore-start -->
```js
useSendMessageBack(): (value: any, text: string, displayText: string) => void
```
<!-- prettier-ignore-end -->

When called, this function will send a `messageBack` activity to the bot.

## `useSendPostBack`

<!-- prettier-ignore-start -->
```js
useSendPostBack(): (value: any) => void
```
<!-- prettier-ignore-end -->

When called, this function will send a `postBack` activity to the bot.

## `useSendTimeoutForActivity`

<!-- prettier-ignore-start -->
```js
useSendTimeoutForActivity(): (activity: Activity) => number
```
<!-- prettier-ignore-end -->

When called, this function will return a function to evaluate the timeout (in milliseconds) for sending a specific activity.

## `useSendTypingIndicator`

<!-- prettier-ignore-start -->
```js
useSendTypingIndicator(): [boolean]
```
<!-- prettier-ignore-end -->

This function will return whether the typing indicator will be sent to the bot when the send box value is being modified.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useSetNotification`

<!-- prettier-ignore-start -->
```js
interface Notification {
  alt?: string;
  id: string;
  level: 'error' | 'info' | 'success' | 'warn' | string;
  message: string;
}

useSetNotification(): (notification: Notification) => void
```
<!-- prettier-ignore-end -->

This hook will return a function which can be called to add or update a notification. If a notification with same ID is already in the system, it will be updated. Otherwise, a new notification will be added.

The `message` field will be processed through an internal Markdown renderer. If Markdown is provided, it is recommended to provide plain text via the `alt` field for assistive technologies.

The toast UI will [debounce notifications](https://github.com/microsoft/BotFramework-WebChat/tree/master/docs/NOTIFICATION.md#postponing-changes-via-debounce) that update too frequently.

## `useShouldSpeakIncomingActivity`

<!-- prettier-ignore-start -->
```js
useShouldSpeakIncomingActivity(): [boolean, (value: boolean) => void]
```
<!-- prettier-ignore-end -->

This function will return a boolean and a function.

1. boolean: whether the next incoming activity will be queued for text-to-speech
1. function: a setter function to control the behavior

If the last outgoing message is sent via speech, Web Chat will set this state to `true`, so the response from bot will be synthesized as speech.

## `useStartDictate`

<!-- prettier-ignore-start -->
```js
useStartDictate(): () => void
```
<!-- prettier-ignore-end -->

This function will open the microphone for dictation. You should only call this function via a user-initiated gesture. Otherwise, the browser may block access to the microphone.

## `useStopDictate`

<!-- prettier-ignore-start -->
```js
useStopDictate(): () => void
```
<!-- prettier-ignore-end -->

This function will close the microphone. It will not send the interims to the bot, but leave the interims in the send box.

## `useStyleOptions`

<!-- prettier-ignore-start -->
```js
useStyleOptions(): [StyleOptions]
```
<!-- prettier-ignore-end -->

This function will return the style options. UI components should honor the styling preferences.

The value is not the same as the props. Web Chat will merge the style options passed in props with default values specified in [`defaultStyleOptions.js`](https://github.com/microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/defaultStyleOptions.js).

To modify the value of `styleOptions` state, change the props you pass to Web Chat.

## `useStyleSet`

<!-- prettier-ignore-start -->
```js
useStyleSet(): [StyleSet]
```
<!-- prettier-ignore-end -->

This function will return the style set.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useSubmitSendBox`

<!-- prettier-ignore-start -->
```js
useSubmitSendBox(): () => void
```
<!-- prettier-ignore-end -->

This function will send the text in the send box to the bot and clear the send box.

## `useSuggestedActions`

<!-- prettier-ignore-start -->
```js
useSuggestedActions(): [CardAction[], (CardAction[]) => void]
```
<!-- prettier-ignore-end -->

This function will return an array and a setter function.

1. array: a list of suggested actions that should be shown to the user
1. function: a setter function to clear suggested actions. The setter function can only be used to clear suggested actions, and it will accept empty array or falsy value only.

The suggested actions are computed from the last message activity sent from the bot. If the user posts an activity, the suggested actions will be cleared.

## `useTimeoutForSend`

<!-- prettier-ignore-start -->
```js
useTimeoutForSend(): [number]
```
<!-- prettier-ignore-end -->

This function will return the interval of time paused before a sending activity is considered unsuccessful. The interval is represented in milliseconds. Due to network partitioning problems, activities that fail to send may eventually be successfully delivered to the bot.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useUserID`

<!-- prettier-ignore-start -->
```js
useUserID(): [string]
```
<!-- prettier-ignore-end -->

This function will return the user ID.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useUsername`

<!-- prettier-ignore-start -->
```js
useUsername(): [string]
```
<!-- prettier-ignore-end -->

This function will return the username.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useVoiceSelector`

<!-- prettier-ignore-start -->
```js
useVoiceSelector(activity: Activity): (voices: SpeechSynthesisVoice[]) => SpeechSynthesisVoice
```
<!-- prettier-ignore-end -->

This function will return a function that can be called to select the voice for a specific activity.

To modify this value, change the value in the style options prop passed to Web Chat.

## `useWebSpeechPonyfill`

<!-- prettier-ignore-start -->
```js
useWebSpeechPonyfill(): [{
  SpeechGrammarList: SpeechGrammarList,
  SpeechRecognition: SpeechRecognition,
  speechSynthesis: SpeechSynthesis,
  SpeechSynthesisUtterance: SpeechSynthesisUtterance
}]
```
<!-- prettier-ignore-end -->

This function will return the ponyfill for the Web Speech API.

To modify this value, change the value in the style options prop passed to Web Chat.

# Component-specific hooks

These are hooks specific provide specific user experience.

## `MicrophoneButton`

These are hooks that are specific for the microphone button.

-  [`useMicrophoneButtonClick`](#usemicrophonebuttonclick)
-  [`useMicrophoneButtonDisabled`](#usemicrophonebuttondisabled)

### `useMicrophoneButtonClick`

<!-- prettier-ignore-start -->
```js
useMicrophoneButtonClick(): () => void
```
<!-- prettier-ignore-end -->

When called, this function will toggle microphone open or close.

### `useMicrophoneButtonDisabled`

<!-- prettier-ignore-start -->
```js
useMicrophoneButtonDisabled(): () => void
```
<!-- prettier-ignore-end -->

This function will return whether the microphone button is disabled. This is different from `useDisabled()`. The microphone button could be disabled because it is currently starting or stopping.

This value can be partly controllable through Web Chat props.

## `SendBox`

These are hooks that are specific for the send box.

-  [`useSendBoxSpeechInterimsVisible`](#usesendboxspeechinterimsvisible)

### `useSendBoxSpeechInterimsVisible`

<!-- prettier-ignore-start -->
```js
useSendBoxSpeechInterimsVisible(): [boolean]
```
<!-- prettier-ignore-end -->

This function will return whether the send box should show speech interims.

## `TextBox`

These are hooks that are specific to the text box in the send box.

-  [`useTextBoxSubmit`](#usetextboxsubmit)
-  [`useTextBoxValue`](#usetextboxvalue)

### `useTextBoxSubmit`

<!-- prettier-ignore-start -->
```js
useTextBoxSubmit(): (setFocus: boolean) => void
```
<!-- prettier-ignore-end -->

This function will send the text box value as a message to the bot. In addition to the original `useSubmitSendBox` hook, this function will also scroll to bottom and, optionally, set focus to the send box.

The focus is useful for a phone scenario where the virtual keyboard will only be shown when a text box is focused.

### `useTextBoxValue`

<!-- prettier-ignore-start -->
```js
useTextBoxValue(): [string, (value: string) => void]
```
<!-- prettier-ignore-end -->

This function will return a string and a function.

1. string: the text box value
1. function: the setter function to set the text box value.

The setter function will call the setter of [`useSendBoxValue`](#usesendboxvalue) and also stop dictation if started.

## `TypingIndicator`

These are hooks that are specific to the typing indicator.

-  [`useTypingIndicatorVisible`](#usetypingindicatorvisible)

### `useTypingIndicatorVisible`

<!-- prettier-ignore-start -->
```js
useTypingIndicatorVisible(): [boolean]
```
<!-- prettier-ignore-end -->

This function will return whether the typing indicator should be visible or not. This function is time-sensitive, meaning that the value will change as time passes.

This function derives the visibility of the typing indicator via values from the [`useActiveTyping`](#useactivetyping) hook. Active typing from user is ignored.

## Telemetry

### `useTrackDimension`

<!-- prettier-ignore-start -->
```js
useTrackDimension(): (name: string, data?: string) => void
```
<!-- prettier-ignore-end -->

This function will add, update, or remove a dimension from telemetry. If `undefined` is passed to `data`, the dimension will be removed. This hook will not trigger `onTelemetry` handler.

### `useTrackEvent`

<!-- prettier-ignore-start -->
```js
type EventData =
  number |
  string |
  { [key: string]: number | string };

useTrackEvent(): {
  (name: string, data?: EventData): void;
  debug: (name: string, data?: EventData) => void;
  error: (name: string, data?: EventData) => void;
  info: (name: string, data?: EventData) => void;
  warn: (name: string, data?: EventData) => void;
}
```
<!-- prettier-ignore-end -->

This function will emit an event measurement. When called, the `onTelemetry` handler will be triggered. All numeric data passed to `data` must be a non-negative finite number.

Log levels can be specified using: `debug`, `error`, `info`, `warn`. If log level is not specified, it will default to `info`.

<!-- prettier-ignore-start -->
```js
const MyComponent = () => {
  const trackEvent = useTrackEvent();

  trackEvent('This event will have default of level "info".');

  trackEvent.debug('This event will be of level "debug".');
  trackEvent.error('This event will be of level "error".');
  trackEvent.info('This event will be of level "info".');
  trackEvent.warn('This event will be of level "warn".');
};
```
<!-- prettier-ignore-end -->

### `useTrackException`

<!-- prettier-ignore-start -->
```js
useTrackException(): (error: Error, fatal?: boolean = true) => void
```
<!-- prettier-ignore-end -->

This function will emit an exception measurement. When called, the `onTelemetry` handler will be triggered.

### `useTrackTiming`

<!-- prettier-ignore-start -->
```js
useTrackTiming(): (name: string, fn: function) => void
useTrackTiming(): (name: string, promise: Promise) => void
```
<!-- prettier-ignore-end -->

This function will emit timing measurements for the execution of a synchronous or asynchronous function. Before the execution, the `onTelemetry` handler will be triggered with a `timingstart` event. After completion, regardless of resolve or reject, the `onTelemetry` handler will be triggered again with a `timingend` event.

If the function throws an exception while executing, the exception will be reported to [`useTrackException`](#usetrackexception) hook as a non-fatal error.
