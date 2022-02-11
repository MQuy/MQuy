## Accessibility

- A accessible website means any users can use all its features and content regardless of how they access the web (even with physical or mental impairments).
- Watching [https://www.youtube.com/watch?v=7mqqgIxX_NU](https://www.youtube.com/watch?v=7mqqgIxX_NU)
  - Firefox supports Accessibility (in devtools).
  - There is a [accessibility tree](https://developer.mozilla.org/en-US/docs/Glossary/Accessibility_tree) which is built based on DOM tree.
    - HTML markup is not always identity to DOM tree since a browser can made modification (like add missing close tag, move invalid tag out and later javascript modifies).
  - `[aria-hidden](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-hidden_attribute)` and `[aria-label](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute)`
- Reading [https://developer.mozilla.org/en-US/docs/Learn/Accessibility/What_is_accessibility](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/What_is_accessibility)
  - Accessibility is the practice of making your websites usable by as many people as possible (people with disabilities, people on their phone or with low internet connection).
  - There are four kinds of disabilities: visual, hearing, mobility and cognitive impairments.
- Reading [https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML)
  - Good semantics (right element for the right job).
  - Use clear language (prefer explicit instead of dash 5 to 7 instead 5-7, expand abbreviation, acronyms).
  - Prefer good text link `<p>Whales are really awesome creatures. <a href="whales.html">Find out more about whales</a>.</p>`.
  - Use header, body and caption for tables.
  - Use text alternatives for image, audio and video (like `alt` and `title`). For image with purely visualization, we can use empty `alt` (screen reader ignore an image instead of reading image url if empty `alt`).
  - Prefer actual links instead of handmade link (like button).
  - For external links, we should use [a icon](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/HTML#external_links_and_linking_to_non-html_resources) to indicate it is external.
- Reading [https://developer.mozilla.org/en-US/docs/Learn/Accessibility/CSS_and_JavaScript](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/CSS_and_JavaScript)
  - With CSS and Javascript, we can make any HTML element look like anything but we shouldn't, make use of native semantic elements as much as possible (`h1`, `p`, `ul/li`, ...).
  - Prefer `em/strong` for semantic, and `b/i/u` for visual (the real-world distinction is not that critical since screen reader might treat them the same).
  - For abbreviation `<abbr title="Hypertext Markup Language">HTML</abbr>`.
  - If we are restyle links, keep in mind we should handle standard/visited/activated state, mouse pointer when hovering, and highlight when focused.
  - A good tip to not reply on color (color blind people) for example, required form fields with asterisk.
  - Use unobtrusive javascript (for example, if client-side validation is not working, users can still submit a form).
  - Don't implement features only for mouse evets, support for keyboard events too (for example mouseover/mouseout + focus/blur).
- Reading [https://developer.mozilla.org/en-US/docs/Learn/Accessibility/Mobile](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/Mobile)
  - Responsive design (multi layout doesn't work well, image sizes, higher-resolution images).
  - Not disabling zoom (only disable when needed like UI is broken) `<meta name="viewport" content="width=device-width; user-scalable=yes">`.
  - Prefer native input elements on mobile, they are handled well on those platform (like `type=time/date`) .

### Resources

- [https://developer.mozilla.org/en-US/docs/Learn/Accessibility](https://developer.mozilla.org/en-US/docs/Learn/Accessibility)
- [https://www.w3.org/WAI/standards-guidelines/wcag/glance/](https://www.w3.org/WAI/standards-guidelines/wcag/glance/)
- [https://developer.mozilla.org/en-US/docs/Tools/Accessibility_inspector](https://developer.mozilla.org/en-US/docs/Tools/Accessibility_inspector)
- [https://www.a11yproject.com/checklist/](https://www.a11yproject.com/checklist/)
