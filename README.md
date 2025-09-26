def find_in_shadow_dom(driver, root, selector):
    """Find elements by CSS selector inside a given root (document or shadowRoot)."""
    return driver.execute_script(
        "return arguments[0].querySelectorAll(arguments[1])", root, selector
    )

def click_save_button(driver, file_name: str):
    time.sleep(2)  # allow popup to render

    # start from the main document
    document = driver.execute_script("return document")

    # Step 1: get all download item containers from ANY shadow root
    rows = find_in_shadow_dom(driver, document, "div.download_itemContent")

    for row in rows:
        try:
            # Step 2: get file name from shadow-root of fluent-button
            file_host = row.find_element(By.CSS_SELECTOR, "div.download_widthContainer fluent-button.downloads_itemTitle")
            file_shadow = driver.execute_script("return arguments[0].shadowRoot", file_host)
            filename = file_shadow.text.strip()

            if file_name in filename:  # partial match works too
                print(f"Found file: {filename}")

                # Step 3: locate Save button in footer
                save_host = row.find_element(By.CSS_SELECTOR, "div.footerContainer fluent-button[title='Save']")
                save_shadow = driver.execute_script("return arguments[0].shadowRoot", save_host)
                save_shadow.find_element(By.CSS_SELECTOR, "button").click()

                print(f"✅ Clicked Save for {file_name}")
                return True
        except Exception as e:
            continue

