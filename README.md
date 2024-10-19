# WACustomMentions
Script that enables custom mentions in WhatsApp Web.  
The research was inspired by [Schwartzblatz's WhatsApp-Web Plus extension](https://github.com/Schwartzblat/WhatsApp-Web-Plus/blob/main/scripts/hook_send_message.js) (not a recommendation).

> [!CAUTION] 
> This JavaScript code is for **research purposes** only and is not intended to be used as a basis for any commercial or non-research purposes.  
> The use of this code is at the user's own risk, and the author(s) assume no responsibility for any misuse or unintended consequences.  
>
> **By using this code, the user acknowledges and agrees to the following:**
> 
> - The user is solely responsible for any legal consequences arising from the use of this code.  
> - The user will not hold the author(s) liable for any damages, losses, or legal actions resulting from the use of this code.  
> - The user agrees to comply with any applicable laws and regulations, including but not limited to copyright, intellectual property, and privacy laws.  
> - Furthermore, the user acknowledges that using this code may violate WhatsApp's Terms of Service and could potentially lead to account bans or other penalties.  
> - The user is advised to consult their own legal counsel before using this code to ensure compliance with all applicable laws and regulations.  
## Usage
1. Open WhatsApp Web in your browser.
2. Press F12 to open the developer tools.
3. Go to the `console` tab.
4. Paste the script.
```js
const WAWebSendMsgRecordAction = require('WAWebSendMsgRecordAction');
const { getParticipantRecord } = require('WAWebGroupMsgSendUtils');
const { createUserWid } = require('WAWebWidFactory');

const mentions = {
  '@everyone': 'participants',
  '@admins': 'admins',
  '@custom': ['972501231231', '972501111222']
};

const createMentionWid = num => createUserWid(`${num}@s.whatsapp.net`);

const originalSendMsgRecord = WAWebSendMsgRecordAction.sendMsgRecord;

WAWebSendMsgRecordAction.sendMsgRecord = async function(msg) {
  if (msg?.body && msg.id.remote.server === 'g.us') {
    const matchedTag = Object.keys(mentions).find(tag => msg.body.includes(tag));
    if (matchedTag) {
      const mentionValue = mentions[matchedTag];
      msg.mentionedJidList.push(...(
        Array.isArray(mentionValue) ? mentionValue.map(createMentionWid) :
        (await getParticipantRecord(msg.id.remote.toString()))[mentionValue].map(createUserWid)
      ));
      console.debug(`%c[DEBUG]%c message hooked: ${msg.body}`, 'font-weight: 900; font-size: 16px; color: orange;', '');
    }
  }
  return originalSendMsgRecord.call(this, msg);
};
```
