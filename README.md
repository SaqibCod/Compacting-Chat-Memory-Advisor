# *Compacting Chat Memory Advisor for Spring AI*

This repository provides a custom implementation of a *Spring AI Advisor* designed to manage large language model (LLM) context windows through *automatic summarization*. While standard chat memory advisors often clear out conversation history entirely once a message limit is reached, this project provides a mechanism to *compact* that history into a concise summary, preserving context while optimizing token usage.

## *Overview*
In many AI applications, conversation history is lost once the context window fills up. Inspired by the /compact command found in tools like *Claude Code, this project uses an **AOP-style (Aspect-Oriented Programming)* approach to wrap LLM calls and manage memory dynamically. When a specific threshold is met, a percentage of the existing messages are sent to an LLM to be summarized into a single message, which is then re-inserted into the chat memory.

## *Key Features*
*   *Threshold-Based Compaction:* Automatically triggers summarization when the message count reaches a configurable percentage of the maximum allowed.
*   *Dual-Model Efficiency:* Optimized to use a primary model (e.g., OpenAI) for the main interaction and a cheaper, faster model (e.g., *Google Gemini 2.5 Flash*) specifically for generating summaries.
*   *Intelligent Filtering:* During compaction, the system excludes system messages and focuses on summarizing only the user and assistant interactions.
*   *Token Savings:* By condensing multiple messages (e.g., 15 messages) into a single summary, the system significantly reduces the number of tokens sent in subsequent prompts.

## *Configuration*
The advisor is highly configurable via application properties, allowing you to fine-tune the memory management behavior:

*   max-messages: The maximum number of messages to retain in memory (default is typically 20).
*   compact-threshold: The percentage of max-messages that triggers the compaction process (e.g., 0.78 for 78%).
*   messages-to-compact: The percentage of the conversation history that should be condensed once the threshold is hit.

## *How It Works*
1.  *Monitor:* The advisor checks the message size before each LLM call.
2.  *Evaluate:* If the message count exceeds the compact-threshold, it initiates compaction.
3.  *Summarize:* It retrieves a set number of messages, calls a *Summary Client*, and receives a condensed overview of the conversation.
4.  *Replace:* It clears the old messages from the chat memory and injects the new summary, effectively "clearing" the window while retaining the context.
5.  *Augment:* The current prompt is augmented with the new summary and any remaining un-compacted messages before being sent to the LLM.

*

*Analogy for Better Understanding:*
Imagine you are writing a long story in a notebook with a limited number of pages. Instead of tearing out the first ten pages and throwing them away when you run out of space (standard memory clearing), you write a one-paragraph summary of those pages on a fresh sheet and then discard the originals. You still know everything that happened in the beginning, but you have successfully freed up nine pages to continue your story.
