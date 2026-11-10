# Automation Anywhere A360 -- Excel Paste Special Window Recognition Failure

**Category:** Excel Advanced\
**Platform:** Automation Anywhere A360\
**Issue type:** Runner-specific support issue\
**Status:** Resolved\
**Date:** 10 November 2026

## 1. Problem statement

An Automation Anywhere bot could not consistently open or identify the
Excel **Paste Special** dialog on a specific runner machine. The same
automation worked on another machine.

The bot copied data between dynamically named Excel workbooks. Multiple
Excel application sessions were open on the runner, making it difficult
to identify and activate a particular workbook by its window title.

## 2. Environment and context

-   Automation Anywhere A360
-   Excel Advanced package
-   Microsoft Excel
-   Runner machine with multiple Excel sessions
-   Dynamically named workbooks processed one after another from email
    files
-   Data copied from one workbook and pasted into another

## 3. Expected behaviour

1.  Copy the required data from the source workbook.
2.  Activate the appropriate destination Excel window.
3.  Open the Paste Special dialog.
4.  Select the required paste option.
5.  Complete the operation and continue processing.

## 4. Observed behaviour

-   The bot failed to capture or detect the Paste Special dialog on the
    affected runner.
-   The workflow worked on another machine.
-   Multiple Excel sessions made window activation and identification
    difficult.
-   Workbook names were dynamic, so hardcoding a workbook title was not
    a suitable solution.
-   The runner did not consistently open the Paste Special dialog
    through the original capture-and-click approach.

## 5. Investigation observations

The troubleshooting identified two main challenges:

  -----------------------------------------------------------------------
  Area                                Observation
  ----------------------------------- -----------------------------------
  Paste Special dialog recognition    The dialog was not consistently
                                      detected on the affected runner.

  Multiple Excel sessions             Selecting and activating the
                                      intended Excel window was
                                      difficult.

  Dynamic workbook names              A fixed window title could not be
                                      used reliably.

  Machine-specific behaviour          The same workflow worked on another
                                      machine.
  -----------------------------------------------------------------------

These are observations from the incident; they do not establish a
confirmed underlying Excel or Windows root cause.

## 6. Resolution implemented

A fallback flow was added so the bot did not rely on a single
window-capture method.

### Step 1 -- Check whether Paste Special exists

Use the **Window Exists** action to check whether the Paste Special
dialog is present.

### Step 2 -- First fallback: activate the current window and use the shortcut

If the Paste Special window does not exist:

1.  Activate the currently active Excel window using the available
    window variable.
2.  Use **Simulate Keystrokes** to send `Ctrl + Alt + V`.
3.  Wait for 3 seconds.

### Step 3 -- Second fallback: image recognition

If the Paste Special window is still not detected:

1.  Use **Image Recognition**.
2.  Enable the **Window in Window** option.
3.  Identify and click the **Paste** option in the dialog.
4.  Use **Simulate Keystrokes** to send the `S` key.
5.  Continue with the Paste Special operation.

> **Important:** The keystroke used in this implementation is `S` (the
> letter S), not the word `Yes`.

### Step 4 -- Continue execution

After the Paste Special interaction, allow the bot to continue with the
remaining workflow and subsequent email/workbook processing.

## 7. Logical flow

``` text
Start Paste Special operation
        |
Check whether Paste Special window exists
        |
If it does not exist
        |
Activate currently active Excel window
        |
Simulate Ctrl + Alt + V
        |
Wait 3 seconds
        |
Check whether Paste Special window exists again
        |
If it is still not detected
        |
Image Recognition (Window in Window)
        |
Click Paste option
        |
Simulate S
        |
Continue bot execution
```

This is a logical representation of the described fallback. Implement
the conditions in the bot to match the actual Window Exists checks and
branches.

## 8. Validation and result

After implementing the fallback:

-   The Paste Special interaction succeeded on the previously affected
    runner.
-   The bot was able to proceed with execution.
-   The solution did not require hardcoding the dynamic workbook name
    for this interaction.
-   The bot continued processing.

**Outcome: Resolved in the tested workflow.**

## 9. Preventive recommendations

1.  Avoid hardcoding dynamic workbook titles.
2.  Validate which Excel window is active before sending keyboard
    shortcuts.
3.  Use window-based detection first where it is reliable.
4.  Keep keyboard simulation as a fallback.
5.  Add an appropriate wait after opening the dialog.
6.  Use image recognition as a secondary fallback and ensure the target
    image is stable.
7.  Validate the workflow on the actual runner where the issue occurs.
8.  Where possible, verify that the paste operation completed before
    continuing.

## 10. Screenshots to add

Capture screenshots from the actual Automation Anywhere bot and runner.
Add them to a `screenshots/Excel/` folder in the repository.

  -----------------------------------------------------------------------
  Suggested filename                  Screenshot content
  ----------------------------------- -----------------------------------
  `01-original-implementation.png`    Original Paste Special capture
                                      approach

  `02-window-exists-condition.png`    Window Exists condition

  `03-activate-excel-window.png`      Activate Window configuration

  `04-keyboard-shortcut.png`          `Ctrl + Alt + V` simulation

  `05-wait-configuration.png`         3-second Wait action

  `06-image-recognition.png`          Image Recognition with Window in
                                      Window enabled

  `07-paste-and-s-key.png`            Paste selection and `S` keystroke

  `08-successful-execution.png`       Successful bot execution
  -----------------------------------------------------------------------

Use real screenshots from your environment. Remove or blur email
addresses, customer data, workbook contents, credentials, and other
sensitive information before committing them.

## 11. Suggested GitHub commit

``` text
docs: add A360 Excel Paste Special runner issue resolution
```

## 12. Suggested repository location

``` text
AutomationAnywhere-Support/
└── Excel/
    └── Paste-Special-Window-Recognition-Failure.md
```

------------------------------------------------------------------------

*Documented from a real-world Automation Anywhere A360 support incident.
The resolution reflects the steps reported for the tested workflow.*
