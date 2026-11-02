# Make Enter insert a newline in Codex for Mac

[日本語](README.md) | English

This guide uses Karabiner-Elements to change the message input keys in the Codex desktop app for macOS.

| Key | Action |
| --- | --- |
| Enter | Insert a newline (sends Shift + Enter) |
| Command + Enter | Send the message (sends Enter) |

The rule remaps keys while Codex is the frontmost application. **It is not a Codex plugin or an official feature.** It uses a separate macOS utility. Windows is not covered.

## Before you begin

- The rule applies throughout the Codex application. Enter may also behave differently outside the message composer.
- It may affect Enter while confirming Japanese text conversion or selecting an IME candidate. Test your input method and remove the rule as described below if it causes problems.
- The newline and send behavior was confirmed on the author's Mac, but has not been verified on every environment or input method.
- Other Karabiner-Elements rules or future Codex updates may affect the behavior.

## Setup

### 1. Install Karabiner-Elements

1. Download and install the app from the [official Karabiner-Elements website](https://karabiner-elements.pqrs.org/).
2. Open Karabiner-Elements. If macOS asks you to configure permissions, follow the on-screen instructions. Permissions are required to modify keyboard input. See the [official macOS settings guide](https://karabiner-elements.pqrs.org/docs/manual/misc/required-macos-settings/) for details.

### 2. Add the Codex rule

1. Open **Complex Modifications** in Karabiner-Elements settings.
2. Select **Add your own rule**. The label may vary by version.
3. Delete all of the sample text in the editor.
4. Copy the entire JSON below, paste it into the editor, and click **Save**.

```json
{
  "description": "Codex: Enter for newline, Command+Enter to send",
  "manipulators": [
    {
      "type": "basic",
      "from": {
        "key_code": "return_or_enter",
        "modifiers": {
          "mandatory": ["command"],
          "optional": ["caps_lock"]
        }
      },
      "to": [{ "key_code": "return_or_enter" }],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": ["^com\\.openai\\.codex$"]
        }
      ]
    },
    {
      "type": "basic",
      "from": {
        "key_code": "return_or_enter",
        "modifiers": {
          "optional": ["caps_lock"]
        }
      },
      "to": [
        {
          "key_code": "return_or_enter",
          "modifiers": ["left_shift"]
        }
      ],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": ["^com\\.openai\\.codex$"]
        }
      ]
    }
  ]
}
```

The same rule is also available in [codex-enter-rule.json](codex-enter-rule.json). See the [official instructions for adding a rule](https://karabiner-elements.pqrs.org/docs/manual/configuration/add-your-own-complex-modifications/) if needed.

### 3. Test the behavior

Return to Codex and use a short test message to check the following:

1. **Enter** inserts a newline without sending.
2. **Command + Enter** sends the message.
3. If you use Japanese or another IME, candidate selection and confirmation work as expected.
4. Enter works acceptably in other parts of Codex you use, such as search fields and menus.
5. Enter still works normally in other applications.

If anything behaves unexpectedly, remove the rule. IME behavior may vary by input method.

## Disable or remove the rule

Open Karabiner-Elements, go to **Complex Modifications**, find `Codex: Enter for newline, Command+Enter to send`, and remove it. This stops the key remapping performed by this rule.

## Troubleshooting

- **Nothing changes:** Check that the rule was saved, Karabiner-Elements is running, and the required macOS permissions are enabled.
- **The rule works outside Codex or does not work in Codex:** The rule targets the application bundle identifier `com.openai.codex`. Check the identifier for your app in [Karabiner-EventViewer's Frontmost Application tab](https://karabiner-elements.pqrs.org/docs/json/complex-modifications-manipulator-definition/conditions/frontmost-application/).
- **Menus or IME conversion are affected:** Remove the rule. It does not currently distinguish the composer from other controls or detect active text composition.
- **Keys behave unexpectedly:** Check for conflicts with other Complex Modifications and review their order.

## How it works

When the foreground application's bundle identifier is `com.openai.codex`, Karabiner-Elements transforms Enter into Shift + Enter and Command + Enter into an unmodified Enter. The rule relies on Codex interpreting those keys as newline and send, respectively. It does not apply to other applications.
