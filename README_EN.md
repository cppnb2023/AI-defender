**Core Problem:**  

Current malware defense systems exhibit critical shortcomings in countering threats written in **obscure/scripting languages** (e.g., Lua, Tcl, Lisp, Python). Traditional antivirus software primarily relies on static signature matching, yet such malware easily evades detection through:  

1.  **Dynamic Function Registration:** Leveraging host languages (e.g., C/C++) to register malicious functions with arbitrary names for disguise.  
2.  **Logic Splitting & Obfuscation:** Fragmenting malicious behaviors across multiple seemingly benign functions/scripts.  
3.  **Interpreter Bundling:** Statically compiling script interpreters (e.g., Lua) into malicious EXEs while storing malicious logic externally as scripts, drastically reducing static scan detection rates.  
4.  **Complex Memory Behavior:** Innately intricate memory operations of language interpreters impede precise identification of malicious intent via memory monitoring.  
5.  **Fragmented Obscure-Language Ecosystem:** Security vendors struggle to develop deep detection logic for the vast and ever-growing array of niche language interpreters.  

**Project Goal:**  

To build a **localized, intelligent, and user-participatory** active defense system focused on **real-time behavior monitoring** and **threat intent assessment**, effectively combating malware leveraging obscure languages or advanced evasion techniques.  

**Core Solution:**  

1.  **Deep Behavior Monitoring:**  
    *   Utilize **ETW (Event Tracing for Windows)** as the core data source to continuously capture system-wide critical events—API calls, file operations, network activity, registry access, process/thread behavior—with minimal overhead.  
    *   **Focus on core function call sequences and context** rather than static code signatures.  

2.  **Local AI Threat Assessment Engine:**  
    *   Embed a lightweight, locally run **AI model** (e.g., decision trees, random forests, lightweight neural networks, or rule engines).  
    *   This engine **continuously learns and analyzes** massive ETW behavioral data daily.  
    *   **Core Function:** Dynamically computes a **"Threat Score"** for each active process.  
    *   **Assessment Output:** Classifies process behavior into:  
        *   **Safe:** Clear, expected behavior patterns.  
        *   **Unknown:** Uncommon patterns or insufficient evidence; requires user attention.  
        *   **Malicious:** Patterns highly aligned with known threats or exhibiting critical risk combinations.  

3.  **User Empowerment & Customization:**  
    *   **Custom Detection Rules & Weighting:** Users define specific behavioral patterns (e.g., `CreateRemoteThread` + `WriteProcessMemory` + connecting to suspicious IPs) and assign weights. The system identifies these patterns during threat scoring, cumulatively adjusting the final classification.  
    *   **Automated Response:** For processes judged **Malicious**, users can author or select predefined **"Response Hooks"** to auto-execute actions (e.g., terminate process, quarantine files, block network traffic, trigger alerts).  
    *   **Handling Unknown Programs:** For **Unknown** processes, the system alerts users with detailed behavior reports, **enabling contextual user judgment** to decide actions (allow, block, tag rules).  

4.  **Technology Stack & Philosophy:**  
    *   **Languages: Lisp (SBCL) + C**  
        *   **Lisp (SBCL):** As "AI’s ancestral language," its **symbolic processing power, macro system, runtime introspection, and hot-swapping capabilities** make it an **ideal core** for building flexible, dynamically extensible rule engines, AI model interpreters, and user-customization interfaces. SBCL delivers exceptional native execution speed.  
        *   **C:** Powers **high-performance ETW data collection, low-level parsing, memory operations, and OS interactions**, ensuring monitoring efficiency and response speed.  
    *   **Core Philosophy: Democratizing Security**  
        *   **Beyond Expert-Defined Rules:** Security should not be solely dictated by vendor engineers. Users best understand their own environments and expected software behaviors.  
        *   **Users as the Frontline:** By providing robust **behavioral data** and **custom rule/response capabilities**, users become active participants in their defense, countering "long-tail" threats (especially obscure-language and 0-day attacks) overlooked by vendor rules.  

**Key Advantages:**  

*   **Neutralizes Obscure-Language Threats:** Analyzes behavior directly, ignoring language specifics and obfuscation.  
*   **Circumvents Static Detection:** Signature-independent; effective against interpreter bundling and external scripts.  
*   **Intelligent Assessment:** Leverages local AI to continuously learn behaviors and dynamically evaluate threat intent.  
*   **High Customizability:** User-defined rules and response strategies adapt to unique needs.  
*   **Proactive Defense:** Real-time monitoring with automated malicious-behavior responses.  
*   **User Empowerment:** Returns security decision-making power to users, achieving "security democratization."  
*   **Efficient Tech Stack:** Lisp enables flexibility and AI affinity; C guarantees underlying performance.  

**Usage Scenario:**  

1.  A user detects suspicious activity (e.g., document directory encryption attempts) by a Tcl-based tool.  
2.  Aegis Sentinel captures its behaviors via ETW: `File traversal` + `Non-standard encryption API calls` + `Connecting to C&C IP`.  
3.  The built-in model, combined with a user-defined `"Encrypt documents + External call"` rule (high weight), classifies it as **Malicious**.  
4.  The system triggers a preset hook: Terminates the Tcl process, quarantines files, and issues an alert.  
5.  The user confirms it as ransomware via the alert and chooses permanent deletion.  

**Core Slogan:**  
> **"Aegis Sentinel: See through the behavioral essence, intelligently fend off unknown threats, empower users to command security!"**  
