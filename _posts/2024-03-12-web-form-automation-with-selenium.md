## Intro to Seleniuum
Selenium is a powerful tool used for automating web browsers. It provides a way to interact with web elements on a webpage programmatically, allowing users to automate various tasks such as filling out forms, clicking buttons, and navigating through web pages. Selenium is widely used for web application testing, web scraping, and web automation tasks. Compared to other tools, Selenium stands out for its flexibility and robustness. It supports multiple programming languages such as Python, Java, C#, and JavaScript, making it accessible to developers with different backgrounds. Additionally, Selenium works with all major web browsers, including Chrome, Firefox, Safari, and Edge, providing a consistent experience across different platforms.



## Intro to WMS
Warehouse Management Systems (WMS) are pivotal in managing warehouse operations. They enhance the efficiency of processes such as material tracking, receiving, picking, packing, and shipping. In contexts where our inventory consists of standard 24-foot containers, a WMS becomes crucial for monitoring material flow. However, manual data entry into a WMS is laborious. By integrating Selenium, we automate repetitive tasks such as inventory queries, receipt creation, and order generation, thereby streamlining warehouse operations.

## Process Flow
We encapsulate automation tasks within a Python class, WMS, designed to interact with our warehouse management system. This class includes methods for setting up Selenium WebDriver, managing user authentication, inventory querying, processing of inbound and outbound orders, and integrating with Google Drive for data handling. Below, I will walk you through the essential methods of this class, providing insights into how each contributes to warehouse automation:
```
class WMS:
    def __init__(self):
        # Initial set up of the WMS clas
        return True

    def setup_driver(self):
        # Sets up the selenium driver
        return True

    def close_driver(self):
        # Close the selenium driver 
        return True
    
    def login():
        # log in to the WMS page by filling out authentication details
        return True

    def query_inventories(self):
        # query the existing inventories by navigating and clicking in the WMS pages
        return True

    def create_inbound(self):
        # create one/many inbound receipts by navigating and clicking in the WMS pages
        return True

    def create_outbound(self):
        # create one/many outbound orders by navigating and clicking in the WMS pages
        return True
        
    def upload_to_gdrive(self):
        # 
        return True
```

## Page navigations
To interact with elements on a webpage using Selenium, we typically find elements by their ID or name attributes. We can then perform actions such as clicking buttons or entering text into input fields. In Chrome, we can inspect elements by right-clicking on them and selecting "Inspect". Selenium provides a comprehensive API for interacting with web elements, making it easy to automate web interactions programmatically.

## Driver setup
In the setup_driver method, we initialize the Chrome WebDriver and configure essential options, such as the default download directory. This configuration is crucial to ensure the WebDriver functions correctly for our automation needs:
```
def setup_driver(self):
    if not os.path.exists(self.download_dir):
        os.makedirs(self.download_dir)
    chrome_options = Options()
    chrome_options.add_experimental_option("prefs", {
        "download.default_directory": self.download_dir,
        "download.prompt_for_download": False,
        "download.directory_upgrade": True,
        "safebrowsing.enabled": True
    })
    service = Service(ChromeDriverManager().install())
    self.driver = webdriver.Chrome(service=service, options=chrome_options)
```
Here, self.download_dir specifies the directory for downloaded files, which the script creates if it doesn't exist. We configure chrome_options to automate download settings and ensure smooth file handling during automation.

## Log in
The login method streamlines the login process, handling input of credentials and validation of successful authentication:
```
def login(self):
    try:
        self.driver.get(self.login_url)
        username_input = WebDriverWait(self.driver, 2).until(
            EC.visibility_of_element_located((By.NAME, self._username)))
        password_input = WebDriverWait(self.driver, 2).until(
            EC.visibility_of_element_located((By.NAME, self._password)))
        login_button = WebDriverWait(self.driver, 2).until(
            EC.element_to_be_clickable((By.NAME, self._login)))

        username_input.send_keys(self.username)
        password_input.send_keys(self.password)
        login_button.click()

        WebDriverWait(self.driver, 2).until(
            EC.visibility_of_element_located((By.ID, self._login_status)))
        print("Login successful.")
        return True
    
    except Exception as e:
        print(f"Login failed: {str(e)}")
        return False
```
In this method, WebDriverWait combined with expected conditions (EC) ensures that elements are visible and interactable before proceeding, enhancing the reliability of the login process.

## Query the inventories
The query_inventory method automates inventory searches, handling navigation, and file handling:
```
def query_inventory(self):
    try:
        
        self.driver.get(self.inbound_url)
        find_btn = WebDriverWait(self.driver, 2).until(
            EC.element_to_be_clickable((By.NAME, self._search)))
        find_btn.click()

        WebDriverWait(self.driver, 2).until(
            EC.presence_of_element_located((By.ID, self._list)))

        export_btn = WebDriverWait(self.driver, 2).until(
            EC.element_to_be_clickable((By.NAME, self._export)))
        export_btn.click()

        time.sleep(10)

        new_filename = 'inventories.xls'
        os.rename(os.path.join(self.download_dir, 'SearchResults.xls'), os.path.join(self.download_dir, new_filename))
        return True

    except Exception as e:
        return False
```
This method automates clicking the search button, waiting for the search results, and exporting them for further processing, significantly reducing manual work.

## Inbound and Outbound Order Creation
The methods create_inbound and a similar method for creating outbound orders automate form completion for new warehouse receipts or orders. They interact with web elements to input necessary data and submit forms:
```
def create_inbound(self, container='', rev=''):
        if rev == 'K':
            # open a -K 22 pallets receipt
            self.driver.get(self._receipt_base_K_22)
        elif rev == 'D':
            # open a -D 22 pallets receipt
            self.driver.get(self._receipt_base_D_22)
        else:
            return False

        # click copy 
        copy = WebDriverWait(self.driver, 2).until(
            EC.element_to_be_clickable((By.NAME, self._copy)))
        copy.click()
        
        # update the receipt
        receive_ref = WebDriverWait(self.driver, 2).until(
            EC.visibility_of_element_located((By.NAME, self._receive_ref_name)))
        receive_ref.clear()
        receive_ref.send_keys(container)

        # update booking date
        receive_date = WebDriverWait(self.driver, 2).until(
            EC.visibility_of_element_located((By.NAME, self._receipt_date_name)))
        receive_date.clear()
        receive_date.send_keys(self._today)

        # update 22 fields of container number
        for each in self._containers_name:
            container_number = WebDriverWait(self.driver, 2).until(
                EC.visibility_of_element_located((By.NAME, each)))
            container_number.clear()
            container_number.send_keys(container)

        # save
        save = WebDriverWait(self.driver, 2).until(
            EC.element_to_be_clickable((By.NAME, self._save_name)))
        save.click()
        time.sleep(3)
        return True
```
Here, we programmatically fill in the forms for new inbound receipts based on the container type and receipt details, streamlining the process significantly.


## Upload to google drive
Lastly, the upload_to_gdrive method facilitates the uploading of files to Google Drive, enabling easy data backup and sharing:

```
    def upload_to_gdrive(self):
        # Authenticate and create the service
        credentials = service_account.Credentials.from_service_account_file(self.service_account_file, scopes=self.scopes)
        # print(credentials)
        service = build('drive', 'v3', credentials=credentials)

        # Upload the file
        try:
            media = MediaFileUpload(self.file_path, mimetype=self.mime_type) 
            file = service.files().update(fileId=self.file_id, media_body=media).execute()
            # print('Updated File ID: %s' % file.get('id'))
            return True
        except:
            return False
```
This method automates authentication and file uploading, leveraging the Google Drive API to ensure that warehouse data is backed up and accessible.

