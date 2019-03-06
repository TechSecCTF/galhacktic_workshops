Challenges covered in the workshop can be found in the `ida_workshop_chals` folder.

The link to the video is [here.](https://www.youtube.com/watch?v=Zt3vPVwd0Dc)

# Team Strategy

We will be designating a triage person for each CTF - Rahul
Responsibilities for triage person
* Monitor challenge drops
* Pop all challenges open, scope them out
* Keep spreadsheet of challenges and make sure people are updating it
* Allocate people to problems and re-assign people to tasks

## Discord
* Separate channel for each category
* People should be responsible for making sure that their shit is posted to the channel - scripts, idbs.
* Every three hours team check-in. You need to talk about the problem you're working on, progress, issues you're encountering. Take side discussions offline, check-ins very quick. You can also write down summary one sentence, someone encountering bug.

People can come over, stay over for the whole weekend of the CTF, we will be ordering food during the duration of the CTF.

Goal #1: never let there be a challenge that we never look at but could have solved.

Goal #2: When necessary, allocate enough power onto a difficult, must-solve problem.

# Reversing with IDA

Commands used in workshop:
`/` and `;` - add comments
`Tab` - pseudocode
`x` - cross-reference
`n` - rename variable/function
`y` - change type declaration
**Conversions**:
`q` - convert to quad-word
`a` - convert to ASCII
`c` - convert to code
`r` - character
`u` - undefine

`\` - Toggle casts
`Ctrl-w` - save
`Ctrl-shift-w` - Create database snapshot

**Subviews:**
`Local types`
`Structures`
`Strings`

## Creating structures

There are several ways to do this.
fully manual:
1. go to `Structures` subview and `Ins` to create structure
2. Right-click structure, create `Array`
3. In `Convert to Array` window, give it a large-enough size and uncheck `Use dup construct` and `Create as array`
4. You should have a list of unnamed `field_X` bytes in your struct now. Rename them as you reverse!

From C:
1. `View->Open Subviews->Local Types`
2. Add a struct in C syntax, using `stdint` types such as `uint64_t`. Ex.
```
struct kvm_dtable_ray {
    uint64_t base;
    uint16_t limit;
    uint16_t padding[3];
};
```
Alternatively, you can directly parse a C header with the `Ctrl-F9` hotkey.

IDA can also import type libraries in several ways: automatically, if you define a type as a known Linux struct symbol such as `struct kvm_sregs`, or manually via FLIRT signatures, that can match on Windows kernel functions.

**FLIRT**:
FLIRT tries to match up libraries with the assembly code based on some heuristics.
`Subviews->Signatures` shows loaded sigs

For Windows binaries, try `vc32_14` and `vc32ucrt`, or the 64-bit versions, to autodetect Windows Visual C++ functions.

**More links**:
[The ultimate IDA Pro book](https://repo.zenk-security.com/Reversing%20.%20cracking/The%20IDA%20Pro%20Book-2nd%20Edition-2011.pdf)
[IDA hotkeys cheatsheet](https://www.hex-rays.com/products/ida/support/freefiles/IDA_Pro_Shortcuts.pdf)

## Cpp Reversing

See [CTF pwn tips](https://github.com/TechSecCTF/CTF-pwn-tips/wiki/Cpp-Reversing) and linked blog post for info on vtables and identifying constructors/destructors.
  
## Further material:

### IDAPython scripting

A useful thing you might want to do is dump memory/registers at a specific breakpoint that is hit many times.

Here's the stub for doing that:
```python
class DbgHook(DBG_Hooks):
    def dbg_bpt(self, tid, ea):
        [your action here]

debugger = DbgHook()
debugger.hook()

loc_of_interest = ...
AddBpt(loc_of_interest)

# If you don't want the program to stop every time the breakpoint is hit, add this line
# SetBptAttr(loc_of_interest, BPTATTR_FLAGS, 0x8)
```

When you run the debugger (hotkey `F9`), the action will be performed at every breakpoint hit.

IDA Python resource links:
https://researchcenter.paloaltonetworks.com/2016/01/using-idapython-to-make-your-life-easier-part-3/
https://reverseengineering.stackexchange.com/questions/13383/how-can-i-set-breakpoint-and-get-value-of-a-register-with-idapython
http://magiclantern.wikia.com/wiki/IDAPython/intro

Also, chapter 11 in [Gray Hat Python](https://repo.zenk-security.com/Magazine%20E-book/Gray%20Hat%20Python%20-%20Python%20Programming%20for%20Hackers%20and%20Reverse%20Engineers%20(2009).pdf)
