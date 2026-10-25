# Chapter 7, Step 02: Log forensics drill

## Purpose

Practise the diagnosis method on the Step 01 excerpts without reading the printed diagnoses first, so the skill transfers to logs nobody has planted for us. This step produces a written record, not a build. The method it drills is in Chapter 7, section 7.1, the module catalogue it relies on is in section 7.2, and the full drill is in section 7.8.4 of the lesson.

## Procedure

1. Take one fault excerpt from `repo/ch07/step-01-planted-faults` and the lesson sections 7.8.1 to 7.8.3, cover the diagnosis, and write the four method answers in order: the shape, the first unhappy module in time order, the family of starved, flooded, or misaligned, and the one fix we would try first.

2. Repeat for the other two faults.

3. For each fault, write one line of evidence that would disprove our diagnosis if it appeared in the full log, stated as a specific log family from a named module. For Fault 1, for example, a successful codec and QoS print from `le_audio` after the setup errors would change the story entirely.

4. Close the record with a fault card for each fault, in our own words: shape, evidence, cause, fix, rebuild. Six short paragraphs in total across the three faults and their disproof lines.

No commands are required for this step. When a fault card calls for a rebuild, the commands are the pair recorded in `repo/ch07/step-01-planted-faults/README.md`, run from `~/ncs-course` with the toolchain environment loaded.

## Expected result

A lab record in which every diagnosis names a shape and a module before it names a cause. An answer that starts with a cause does not pass, however good the cause is. Each of the three faults has a specific disproof line, and the record ends with the three fault cards. Reading a cold log in this order, shape, module, family, fix, is the skill this course expects us to carry to the bench in the hardware pass and into Act 2.
