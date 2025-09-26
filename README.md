# NewRepo

# Step 1: find all download item containers
    rows = driver.find_elements(By.CSS_SELECTOR, "div.download_itemContent")

    for row in rows:
        try:
            # Step 2: get the filename from shadow-root of fluent-button
            file_host = row.find_element(By.CSS_SELECTOR, "div.download_widthContainer fluent-button.downloads_itemTitle")
            file_shadow = driver.execute_script("return arguments[0].shadowRoot", file_host)
            filename = file_shadow.text.strip()

            # Step 3: check filename
            if file_name in filename:
                print(f"Found file: {filename}")

                # Step 4: locate Save button in footer
                save_host = row.find_element(By.CSS_SELECTOR, "div.footerContainer fluent-button[title='Save']")
                save_shadow = driver.execute_script("return arguments[0].shadowRoot", save_host)
                save_button = save_shadow.find_element(By.CSS_SELECTOR, "button")

                # Step 5: click Save
                save_button.click()
                print(f"✅ Clicked Save for {file_name}")
                return True
        except Exception as e:
            continue
