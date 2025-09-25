# NewRepo
from selenium import webdriver
from selenium.webdriver.common.by import By
import time

def click_save_button(driver, file_name: str):
    time.sleep(2)  # allow popup render

    # Step 1: find all file name fluent-buttons
    file_buttons = driver.find_elements(By.CSS_SELECTOR, "fluent-button.downloads_itemTitle")

    for file_btn in file_buttons:
        # Each has shadow-root -> get filename text
        file_root = driver.execute_script("return arguments[0].shadowRoot", file_btn)
        text = file_root.text.strip()

        if text == file_name:
            # Found the matching file row
            print(f"Found file: {text}")

            # Step 2: get the parent container (footer with Save button)
            container = file_btn.find_element(By.XPATH, "./ancestor::div[@class='footerContainer grid-footer']")

            # Step 3: inside this container, find the Save button
            save_host = container.find_element(By.CSS_SELECTOR, "fluent-button[title='Save']")
            save_root = driver.execute_script("return arguments[0].shadowRoot", save_host)

            save_button = save_root.find_element(By.CSS_SELECTOR, "button")
            save_button.click()
            print(f"✅ Clicked Save for: {file_name}")
            return True

    print(f"❌ File '{file_name}' not found in list")
    return False


# ---------- Usage ----------
driver = webdriver.Chrome()
driver.get("your_download_page_url_here")

click_save_button(driver, "Void Report_Output (4).xlsx")

