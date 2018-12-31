# 35C3 Junior CTF – Number Error

* **Category:** Misc
* **Points:** variable

## Challenge

> The function assert_number(num: number) is merely a debug function for our Wee VM (WeeEm?). It proves additions always work. Just imagine the things that could go wrong if it wouldn't!
>
> http://35.207.189.79/
> 
> Difficulty estimate: Easy - Medium
>
> ===============================================
>
> Good coders should learn one new language every year.
>
> InfoSec folks are even used to learn one new language for every new problem they face (YMMV).
>
> If you have not picked up a new challenge in 2018, you're in for a treat.
>
> We took the new and upcoming Wee programming language from paperbots.io. Big shout-out to Mario Zechner (@badlogicgames) at this point.
>
> Some cool Projects can be created in Wee, like: this, this and that.
>
> Since we already know Java, though, we ported the server (Server.java and Paperbots.java) to Python (WIP) and constantly add awesome functionality. Get the new open-sourced server at /pyserver/server.py.
>
> Anything unrelated to the new server is left unchanged from commit dd059961cbc2b551f81afce6a6177fcf61133292 at badlogics paperbot github (mirrored up to this commit here).
>
> We even added new features to this better server, like server-side Wee evaluation!
>
> To make server-side Wee the language of the future, we already implemented awesome runtime functions. To make sure our VM is 100% safe and secure, there are also assertion functions in server-side Wee that you don't have to be concerned about.

## Solution

The *Wee* interpreter source code can be found here: `http://35.207.189.79/weelang/weeterpreter.ts`.

The endpoint that can be used to run *Wee* code is the following.

```
http://35.207.189.79/wee/run
```

The assertion that must be exploited is the following.

```Typescript
externals.addFunction(
   "assert_number",
   [{name: "num", type: compiler.NumberType}], compiler.StringType,
   false,
   (num: number) => !isFinite(num) || isNaN(num) || num !== num + 1
       ? "NUMBERS WORK" : flags.NUMBER_ERROR
)
```

The parameter must be a finite number, that is not `NaN` and that is equal to the next one number. The behavior can be [the one that happens](https://stackoverflow.com/questions/307179/what-is-javascripts-highest-integer-value-that-a-number-can-go-to-without-losin) using numbers greater than *MAX_INT*.

For *TypeScript* it is: `9007199254740991`. Using the next one number (i.e. `9007199254740992`) will produce the searched effect.

```
alert(assert_number(9007199254740992))
```

The following Python script can be used to send the payload.

```Python
import requests
import json

url = "http://35.207.189.79/wee/run"
data_structure = """{{ "code": {} }}"""
data_content = """alert(assert_number(9007199254740992))"""

data_content_to_send = json.dumps(data_content)
data_to_send = data_structure.format(data_content_to_send)
print "[*] Payload: '{}'.".format(data_to_send)
response = requests.post(url, data=data_to_send)
print "[*] Response: '{}'.".format(response.text)
```

The flag is the following.

```
35C3_THE_AMOUNT_OF_INPRECISE_EXCEL_SH33TS
```