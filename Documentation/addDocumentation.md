# Adding documentation to your workbench.

The purpose of the `Documentation/` directory is to provide the LLM with direct access to relevant information about your project's topic. By placing datasheets, notes, driver documentation, and other reference material here, the LLM can read and reason over it without needing to search the web, leading to more accurate and context-aware assistance.

## markdown files

Creating `.md` (Markdown) or `.txt` files is a convenient way to capture personal notes and information gathered from external sources, such as tutorials, forum posts, datasheets, or vendor websites.

**Guidelines for writing these notes:**

- **Include the source URL.** Always add a link to the page where you found the information, so you (and others) can verify or revisit the original content.
- **Add a retrieval date.** Websites change or disappear over time. Recording the date you retrieved the information makes it clear how current the content was when you captured it.
- **Keep notes focused.** Write down only what is relevant to your project — paraphrase rather than copy large blocks of text to stay within copyright boundaries.
- **Use descriptive filenames.** Name files after the topic they cover (e.g. `lora-frequency-bands.md`, `ch340-install-windows.md`) so they are easy to find.

**Suggested file header template:**

```markdown
# <Topic title>

Source: [<Page title or description>](<url>)
Retrieved: <YYYY-MM-DD>

---

<Your notes here>
```

**Example:**

```markdown
# TTGO LoRa Series — Pin Mapping Notes

Source: [Xinyuan-LilyGO/TTGO-LoRa-Series](https://github.com/Xinyuan-LilyGO/TTGO-LoRa-Series)
Retrieved: 2026-05-01

---

The TTGO LoRa32 V2.1 uses GPIO 5 for NSS, GPIO 18 for SCK, GPIO 19 for MISO,
and GPIO 27 for MOSI. The DIO0 interrupt pin is on GPIO 26.
```

## PDFs

## GIT repositories. 

### downloaded ZIP (foundation)
Add relevant git repositories to your documentation by downloading them as a ZIP and placing them in the `Documentation/` directory.

1. Find the repository you need. For example: [https://github.com/Xinyuan-LilyGO/TTGO-LoRa-Series](https://github.com/Xinyuan-LilyGO/TTGO-LoRa-Series).
2. On the GitHub repository page, click the green **Code** button and select **Download ZIP**.
3. Once downloaded, extract the ZIP file into the `Documentation/` directory of your project:
   - On Windows: right-click the ZIP → *Extract All…* → browse to `Documentation/`
   - On Linux/macOS: `unzip <repo-name>.zip -d Documentation/`
4. The extracted folder will now be available as local documentation for your workbench.

### Use submodule in git (advanced)
Instead of a static ZIP, you can track an external repository as a git submodule. This keeps the documentation in sync with upstream changes.

1. From the root of your project, add the submodule into the `Documentation/` directory:
   ```bash
   git submodule add <repository-url> Documentation/<folder-name>
   ```
   For example:
   ```bash
   git submodule add https://github.com/Xinyuan-LilyGO/TTGO-LoRa-Series Documentation/TTGO-LoRa-Series
   ```
2. Commit the changes:
   ```bash
   git commit -m "Add driver documentation as submodule"
   ```
3. When cloning your project on another machine, initialise and fetch the submodule:
   ```bash
   git clone --recurse-submodules <your-repo-url>
   # or, if already cloned:
   git submodule update --init --recursive
   ```
4. To update the submodule to the latest upstream commit later:
   ```bash
   git submodule update --remote Documentation/<folder-name>
   git commit -m "Update driver submodule to latest"
   ```
