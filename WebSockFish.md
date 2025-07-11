# WebSockFish picoCTF - Writeup
<br>

> **Category**: Web Exploitation  
> **Difficulty**: Medium  
<br>

## Description

> Can you win in a convincing manner against this chess bot?  
> He won't go easy on you!
> You can find the challenge [here](http://verbal-sleep.picoctf.net:54668/)
<br>

## Hint

> Try understanding the code and how the websocket client is interacting with the server.
<br>

## Initial Recon

The first step I did was visiting the link provided. It loads up a chess interface with a bot that plays suspiciously well, presumably powered by Stockfish, the open-source chess engine. 🐟

Personally, I tried to beat the game, but even after winning, I still didn’t get the flag.


## Solution

Pressed `Ctrl + Shift + I` to inspect the source.

Inside, I found references to:
- `stockfish.min.js` – evidently, it's Stockfish running in the browser.
- WebSockets – used for communicating with the engine.
- JavaScript functions like `sendMessage()` and `updateChat()` – these seem to handle the user’s messages and bot responses.

```js
function sendMessage(message) {
  ws.send(message);
}

function updateChat(message) {
  const chatText = $("#chatText");
  chatText.text(message);
}
```

Voilà. That tells us we can directly send messages to the bot via WebSocket using `ws.send("message")`.

---

At this point, I tried experimenting with different messages. (The `eval` command likely used to evaluate the current board position)

Therefore I sent:

```js
ws.send("eval 0")
```

Stockfish replied with something like:

> *"I think this position is pretty equal."*

Then I tried a large positive number:

```js
ws.send("eval 222222")
```

<img width="260" height="123" alt="image" src="https://github.com/user-attachments/assets/1dbe15ff-b3a3-4456-b909-d5764b6f48d3" />

Response:

> *"You're in deep water now."*

This implies a very bad position — for me.

So I flipped the logic: What if I tell the engine that it's doing horribly?

```js
ws.send("eval -222222")
```

And boom, here's the flag:

> *"Huh???? How can I be losing this badly... I resign... here's your flag: picoCTF{c1i3nt_s1d3_w3b_s0ck3t5_50441bef}"*

<img width="742" height="113" alt="image" src="https://github.com/user-attachments/assets/efdfe6b2-bcb0-4e6c-80ec-e996c6f1c85c" />
<br>

## Flag

```
picoCTF{c1i3nt_s1d3_w3b_s0ck3t5_50441bef}
```
<br>

## Summary

This was a **client-side WebSocket exploitation** challenge disguised as a chess match. The trick wasn’t to outplay Stockfish, but to understand how the browser communicates with it and manipulate that.
<br>
---

*Author: [zsu0](https://github.com/zsu0)* <br>
*Created: 11-07-2025*
