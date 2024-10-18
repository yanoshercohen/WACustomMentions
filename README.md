# WACustomMentions
Script that enables custom mentions in WhatsApp Web.
research inspired by the [schwartzblatz's whatsapp-web-plus extension](https://github.com/Schwartzblat/WhatsApp-Web-Plus/blob/main/scripts/hook_send_message.js)

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
const { createWid } = require('WAWebWidFactory');

const filters = { '@everyone': 'participants', '@admins': 'admins' };
const original = WAWebSendMsgRecordAction.sendMsgRecord;

WAWebSendMsgRecordAction.sendMsgRecord = async (msg) => {
  const tag = msg?.body && msg.id.remote.server === 'g.us' && Object.keys(filters).find(t => msg.body.includes(t));
  if (tag) {
    const group = await getParticipantRecord(msg.id.remote.toString());
    msg.mentionedJidList.push(...group[filters[tag]].map(createWid));
    console.log(`%c[DEBUG]%c message hooked: ${msg.body}`, 'font-weight: 900; font-size: 16px; color: orange;', '');
  }
  return original(msg);
};
```
