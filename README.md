# WACustomMentions
Script that enables custom mentions in WhatsApp Web.  
The research was inspired by [Schwartzblatz's WhatsApp-Web Plus extension](https://github.com/Schwartzblat/WhatsApp-Web-Plus/blob/main/scripts/hook_send_message.js) (not a recommendation).
> [!NOTE] 
> Messages with custom mentions are delivered correctly to the server. However, the local message bubble will not be displayed automatically. Displaying in the chat window requires a manual page refresh. This occurs because the script's change to the message object interferes with the local UI update mechanism.
>
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
(() => {
    const WAWebSendMsgRecordAction = require('WAWebSendMsgRecordAction');
    const getParticipantRecord = require('WAWebGroupMsgSendUtils').getParticipantRecord;
    // Use string concatenation instead of createUserWid
    const createMentionWid = num => `${num}@s.whatsapp.net`;

    const mentions = {
        '@everyone': 'participants',
        '@admins': 'admins',
        '@custom': ['972501231231', '972501112222']
    };

    const originalSendMsgRecord = WAWebSendMsgRecordAction.sendMsgRecord;

    WAWebSendMsgRecordAction.sendMsgRecord = async function(msg) {
        if (msg?.__x_body && msg.__x_id?.remote?.server === 'g.us') {
            const matchedTag = Object.keys(mentions).find(tag => msg.__x_body.includes(tag));
            if (matchedTag) {
                const mentionValue = mentions[matchedTag];
                const jidsToAdd = Array.isArray(mentionValue) ?
                    mentionValue.map(createMentionWid) :
                    (await getParticipantRecord(msg.__x_id.remote.toString()))[mentionValue];
                if (jidsToAdd?.length) {
                    msg.mentionedJidList.push(...jidsToAdd);
                }
            }
        }
        return originalSendMsgRecord.apply(this, arguments);
    };
})();
```
