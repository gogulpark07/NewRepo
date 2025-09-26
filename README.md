# NewRepo

from selenium import webdriver
from selenium.webdriver.common.by import By
import time

def click_save_button(driver, file_name: str):
    time.sleep(2)  # wait for popup render

    # --- Step 1: find the top-level shadow host ---
    # Replace selector below with the actual host you see in DOM (e.g. "downloads-panel" or "div#downloads-item-list")
    popup_host = driver.find_element(By.CSS_SELECTOR, "div#downloads-item-list")  
    popup_root = driver.execute_script("return arguments[0].shadowRoot", popup_host)

    # --- Step 2: get all download item hosts inside ---
    items = popup_root.find_elements(By.CSS_SELECTOR, "fluent-button.downloads_itemTitle")

    for host in items:
        shadow = driver.execute_script("return arguments[0].shadowRoot", host)

        # get filename text from inside shadow-root
        filename = shadow.text.strip()
        if file_name in filename:   # partial match also works
            print(f"Found: {filename}")

            # Step 3: locate the Save button in the same row
            footer = host.find_element(By.XPATH, "./ancestor::div[@class='footerContainer grid-footer']")
            save_host = footer.find_element(By.CSS_SELECTOR, "fluent-button[title='Save']")
            save_shadow = driver.execute_script("return arguments[0].shadowRoot", save_host)

            save_shadow.find_element(By.CSS_SELECTOR, "button").click()
            print(f"✅ Clicked Save for {filename}")
            return True

    print(f"❌ File '{file_name}' not found in downloads popup")
    return False


# ---------- Usage ----------
driver = webdriver.Chrome()
driver.get("your_download_page_url_here")

click_save_button(driver, "Void Report_Output (4).xlsx")

