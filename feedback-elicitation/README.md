## Feedback Elicitation Reusable Dialog
___

Feedback Elicitation is an ACDL Reusable Dialog that gives skill developers an easy way to collect feedback about their skill from customers. Currently, Feedback Elicitation requests and collects a numeric rating from the user.


## Installation
___

1. Install the latest version of [acc (@alexa/acdl)](https://www.npmjs.com/package/@alexa/acdl) globally
 ```
 npm i -g @alexa/acdl
 ```
2. Install [@ac-reusable-dialogs/feedback-elicitation](https://www.npmjs.com/package/@ac-reusable-dialogs/feedback-elicitation)
```
npm i @ac-reusable-dialogs/feedback-elicitation
```

## Overview
___

### Arguments:

| Name |Type | Description |
|---|---|---|
| feedback_prompt | APLA | The APLA response prompt to be used for requesting feedback |
| payload | Thing | Response payload of the previous action called |
| notifyAction | Action | Previous action called before calling the FeedbackElicitation dialog |
						
### Return:
| Type | Description |
|---|---|
| Nothing | `nothing` literal |

** Feedback Elicitation must be called after an action call. It will act as the response after the action then offer up the feedback elicitation action.


## Usage
___

### Basic example:

```
...
import com.ac.reusable.dialogs.feedback.*

sample_apla = apla("../resources/prompts/sample_apla/document.json")

type SamplePayload {
    Number num
}

action Number sampleAction()

...

sample {
    ...
    x = sampleAction()
    GetFeedback(feedback_prompt=sample_apla, payload = SamplePayload {num = x}, notifyAction = sampleAction)
    ...
}
```


### Example skill: 
This is NumberBot, a skill that generates a random number, then collects feedback on the experience

```
namespace com.numberbot

import com.amazon.alexa.ask.conversations.*
import com.amazon.ask.types.builtins.AMAZON.*
import com.amazon.alexa.schema.Nothing
import com.amazon.alexa.schema.Thing
import com.amazon.alexa.schema.Number
import com.ac.reusable.dialogs.feedback.*

numericfeedback_apla = apla("../response/prompts/numericfeedback_apla/document.json")

mySkill = skill(
    locales = [Locale.en_US],
    dialogs = [MainDialog],
    skillLevelResponses = SkillLevelResponses
        {
            welcome = prompts.AlexaConversationsWelcome,
            out_of_domain = prompts.AlexaConversationsOutOfDomain,
            bye = prompts.AlexaConversationsBye,
            reqmore = prompts.AlexaConversationsRequestMore,
            provide_help = prompts.AlexaConversationsProvideHelp
        },
    invocationNames = [ InvocationName 
        {
            locale = Locale.en_US,
            invocationName = "acdl number feedback bot"  
        }
    ]
)

type NumPayload {
    Number num
}

getStart = utterances(["give me a number please"])

action Number getNumber()

//main
dialog Nothing MainDialog {
    sample {
        expect(Invoke, getStart)
        number = getNumber()
        //Feedback Elicitation usage
        GetFeedback(numericfeedback_apla, payload = NumPayload {num = number}, notifyAction = getNumber)  
    }
}
```


### Invalid Examples:
1. There is no action call prior to GetFeedback

```
sample {
    expect(Invoke, getStart)
    //Feedback Elicitation usage
    GetFeedback(numericfeedback_apla, payload = NumPayload {num = num}, notifyAction = getNumber)  
}
```

2. GetFeedback already acts as a response, it cannot be directly followed by an additional response

```
sample {
    expect(Invoke, getStart)
    //Feedback Elicitation usage
    GetFeedback(numericfeedback_apla, payload = NumPayload {num = num}, notifyAction = getNumber)  
    response(sample_apla, Notify {actionName = getNumber}, payload = NumPayload {num = num})
}
```


## Alexa/User Turns example
___

...

<span style="color:cyan">Alexa:</span> Please give this interaction a rating between 1-5, where 5 is the highest rating

<span style="color:lime">User:</span> Two

<span style="color:cyan">Alexa:</span> Thank you for the feedback!

...

___

## License
___
This library is licensed under the Amazon Software License.

