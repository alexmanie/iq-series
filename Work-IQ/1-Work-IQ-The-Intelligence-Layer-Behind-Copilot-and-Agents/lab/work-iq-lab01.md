# Lab 01: The Intelligence Layer Behind Copilot & Agents

## Start Work IQ CLI

You can run the following scripts directly from this cookbook, or you can open a terminal session, for example using the PowerShell terminal or the Bash terminal, depending on the platform where you are running this cookbook.

Run the following command, to accept the end user license agreement:

```
workiq accept-eula
```

Now run the following command:

```
workiq ask -q "Who am I? What is my role in the company?"
```

It will give you back information about who you are in your own Microsoft 365 tenant.
Now let's try with the following prompt:

```
workiq ask -q "When is my next meeting?"
```

Work IQ CLI will give you back detailed information about your next and upcoming meeting, based on your personal agenda in Microsoft 365.

---

## Use Work IQ in GitHub Copilot CLI

You can now run Work IQ inside GitHub Copilot CLI via MCP protocol.

Run the following command, to start GitHub Copilot

```
copilot --banner
```

Enjoy the beauty of the GitHub Copilot welcome banner. If prompted, trust GitHub Copilot to access the files in your current folder.

Now, run the following prompt:

```
Who am I? What is my role in the company?
```

GitHub Copilot will analyze your prompt and determine that the ask_work_iq MCP tool is needed to provide you with an answer. As such, it will ask your consent to use the tool. You can say **1. Yes** to consent it just once, or **2. Yes, and don't ask again for tool "ask_work_iq" from "workiq" in this repo** if you want to consent it from now on. Of course, you can also choose **3. No** and avoid using Work IQ.

If you consented, GitHub Copilot will start processing your request and will talk with the Work IQ MCP server to extract to requested information.

Now let's try with the following prompt:

```
When is my next meeting?
```

GitHub Copilot CLI will still rely on Work IQ MCP to give you back detailed information about your next and upcoming meeting, based on your personal agenda in Microsoft 365.

---

## Use Work IQ via REST

Another option you have, is to consume Work IQ via REST API. In order to try this option, you are going to use the Microsoft Graph Explorer web interface.

### Create a new Copilot Chat Conversation

- Open [Graph Explorer](https://aka.ms/ge)  
- Sign into your target tenant
- Open the **Modify Permissions** tab
- Grant the following permissions to the Graph Explorer application: *Content-Type: application/json*, *Mail.Read*, *People.Read.All*, *OnlineMeetingTranscript.Read.All*, *Chat.Read*, *ChannelMessage.Read.All*, *ExternalItem.Read.All*
- Paste the query below 
- Click **Run query**

```http
POST https://graph.microsoft.com/beta/copilot/conversations
Content-Type: application/json

{}
```

- The response you get, should look like the following one:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": "0d110e7e-2b7e-4270-a899-fd2af6fde333",
  "createdDateTime": "2025-09-30T15:28:46.1560062Z",
  "displayName": "",
  "status": "active",
  "turnCount": 0
}
```

Copy the value of the **id** attribute. It represents the unique **conversationId** of your Copilot Chat conversation.

- Paste the query below and replace the {conversationId} placeholder with the value you just copied
- Click **Run query**

```http
POST https://graph.microsoft.com/beta/copilot/conversations/{conversationId}/chat
Content-Type: application/json

{
  "message": {
    "text": "Who am I? What is my role in the company?"
  },
  "locationHint": {
    "timeZone": "America/New_York"
  }
}
``` 

- The response should be a complex JSON object with the information about who you are and what your role is in your company.

---

## Wrap up

As you can see, regardless if you use the Work IQ CLI, Work IQ MCP tools, or low level REST API request you will always get back a consistent response, because Work IQ guarantees you:

- **Unified surface**: One API contract for all use cases (human and agents)
- **Response Fidelity**: API responses mirror interactive UI responses. Same reasoning, same data grounding, same formatting, same capabilities.
- **Multi-Protocol**: Multiple protocol heads (REST, A2A, MCP) sitting on a common runtime. Each protocol optimized for its use case; all share the same underlying orchestration.

## Where to go next

- **Explore the IQ Series** — Continue learning with [Episode 2: Work IQ: A2A for Context‑Aware, Agentic Experiences](../../2-Work-IQ-A2A-for-Context‑Aware-Agentic-Experiences/) and [Episode 3: Tooling with MCP & Copilot CLI](../../3-Work-IQ-Tooling-with-MCP-and-Copilot-CLI/).