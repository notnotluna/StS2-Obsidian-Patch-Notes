def convert_templates(text):
    def replace_template(match):
        content = match.group(1).strip()
        parts = [p.strip() for p in content.split("|")]
        if not parts:
            return ""

        template_name = parts[0]
        params = parts[1:]

        if not params:
            # Fallback if there are no parameters
            return content

        # Check if this is specifically an "Int" template (case-insensitive)
        if template_name.lower() == "int":
            # Return ONLY the first parameter as plain text, dropping the rest
            return params[0]

        # For all other templates, convert to Wikilinks
        page_title = params[0]
        display_text = params[1] if len(params) > 1 and params[1] else None

        # Ignore display text if it's '2' or matches the target title
        if display_text and display_text != "2" and display_text != page_title:
            return f"[[{page_title}|{display_text}]]"
        return f"[[{page_title}]]"

    pattern = r"\{\{\s*([^{}]+?)\s*\}\}"
    prev_text = None
    while prev_text != text:
        prev_text = text
        text = re.sub(pattern, replace_template, text)
    return text