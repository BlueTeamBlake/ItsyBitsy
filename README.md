# ItsyBitsy  
**Investigating an Incident with ELK**

In this challenge room, we investigate an alert triggered by an IDS regarding potential C2 (Command and Control) communication.

During routine SOC monitoring, an analyst observed an alert from the IDS indicating suspicious activity involving a user named **Browne** from the HR department. A suspicious file was accessed that contained a malicious pattern: `THM{ ________ }`. A week’s worth of HTTP connection logs was pulled for investigation. Due to limited resources, only **connection logs** were ingested — found in the `connection_logs` index in Kibana.

Our goal is to analyze these network logs, identify the C2 communication, extract the file link, and ultimately retrieve the malicious content.

---

### 🔎 1) How many events were returned for the month of March 2022?

This one’s fairly straightforward. Loading the logs with the correct time filter (March 2022) gives us the total number of "hits" or events returned. Just apply a filter on the `@timestamp` field and take note of the event count.

![Event Dashboard](images/question1_answer.png)

---

### 🕵️ 2) What is the IP associated with the suspected user in the logs?

From the initial alert, we know we're looking for traffic related to user **Browne**. Filtering the logs by `source_ip` reveals two IPs — one that appears frequently and another that stands out due to its limited use. That less frequent IP is likely our suspect machine.

![Suspect IP](images/question2_answer.png)

---

### 💾 3) The user’s machine used a legit Windows binary to download a file from the C2 server. What is the name of the binary?

After filtering logs tied to the suspected IP, I found a request with the user agent set as `bitsadmin`. This is a legitimate Windows binary that can be abused to download files — a known LOLBin. In this case, it was used to pull content from **pastebin.com**, which is definitely suspicious.

> ✅ **Answer:** `bitsadmin.exe`

![bitsadmin log](images/question3_answer.png)

---

### 🌐 4) The infected machine connected with a famous file-sharing site, which also acted as a C2 server. What is the name of the file-sharing site?

We saw this in the same event — the host accessed was `pastebin.com`. That’s our C2 endpoint in this scenario.

> ✅ **Answer:** `pastebin.com`

---

### 🔗 5) What is the full URL of the C2 to which the infected host connected?

Using the same log, we see the following fields:
- `host`: `pastebin.com`
- `uri`: `/yTg0Ah6a`

Combine them to get the full URL:

> ✅ **Answer:** `http://pastebin.com/yTg0Ah6a`

---

### 📄 6) A file was accessed on the file-sharing site. What is the name of the file accessed?

Visiting the URL reveals that the content is stored in a text-based format. The paste ID `yTg0Ah6a` is often considered the file name when it's pulled via automated tools like `bitsadmin`.

> ✅ **Answer:** `yTg0Ah6a`  
(*or `secret.txt` if it was explicitly shown in the paste content or URL*)

![File Access Log](images/question5_answer.png)

---

### 🧩 7) The file contains a secret code with the format THM{_____}.

Sure does! When we open the paste, we find the flag inside in the expected format.

> ✅ **Answer:** `THM{SECRET__CODE}`






