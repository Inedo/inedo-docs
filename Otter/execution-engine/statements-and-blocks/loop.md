---
title: Loop Blocks
keywords: otter,execution-engine,operation-groups
sequence: 200
---

Looping over the same series of statements can be useful for both visualization and to removing repetitive statements in a plan. That's where the Loop Block comes in.

Loop Blocks run the set of statements in the block while enumerating over a list. The List can be a variable, a function, or a literal expression. 

For example, we can ensure a directory exists for each service in a variable called `@ServiceNames`. This variable could be defined elsewhere in our plan, or externally as a configuration variable.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/loop-block.png" alt="Loop Block in Otter" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/loop-text.png" alt="loop Text in Otter" />
    </tab>
</tab-block>

### Looping and Numbers {#looping-numbers}

With the built-in `@Range` function, you can loop over numbers just as easily as you might with a c-style "for" loop.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/loop-range-block.png" alt="Loop Block in Otter" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/loop-range-text.png" alt="Loop Block in Otter" />
    </tab>
</tab-block>
