# Taurus Configuration Syntax

### Configuration dictionary có các sections thường sử dụng như:

* **Execution**: phần này là phần chính, khai báo các công cụ sẽ được thực thi trong khi chạy, các kịch bản sẽ sử dụng
* **Reporting**: phân tích và cài đặt báo cáo, danh sách các modules để xử lý kết quả báo cáo
* **Services**: dùng để cấu hình module
* **Scenarios**: các đặc tả kịch bản được gọi từ executions
* **Modules**: danh sách các lớp để tải và cài đặt tương ứng
* **Settings**: một số cài đặt cấp cao nhất
* **Provisioning**: đây là một cài đặt nâng cao, cho phép sử dụng các tài nguyên không phải thuộc local để kiểm tra các tải phân tán, cái này chỉ khả dụng khi đã cài đặt và định cấu hình nhà cung cấp tương ứng
* **Included-configs**: cho phép hợp nhất các tập tin cấu hình bổ sung

### Chi tiết của từng section

* **<u>Execution Settings:</u>**

  * **Executor Types:**

    Có thể khởi chạy không giới hạn số lượng các công cụ như JMeter, Gatling, Grinder v.v..

    Taurus tool có thể sử dụng các công cụ cơ bản khác nhau làm executor trong từng tình huống, các công cụ hiện đang hỗ trợ hiện nay như là: **JMeter, Selenium, Gatling, Grinder, Locust, PBench, Siege, ApacheBenchmark, Tsung, Molotov, JUnit, TestNG, Nose, PyTest, RSpec, Mocha, NUnit, WebdriverIO, Robot, Postman/Newman**

    Default của executor là jmeter và có thể thay đổi trong phần settings

    ```yaml
    settings:
      default-executor: jmeter
    ```

  * **Load Profile:**

    Execution có một số tùy chọn để thiết lập cài đặt load profile, một số các cài đặt khả dụng như:

    * concurrency: Số lượng các người dùng ảo
    * ramp-up: Số thời gian để đạt tới ngưỡng số lượng của concurrency
    * hold-for: Thời gian mà số concurrency vẫn ở mức như vậy
    * iterations: Giới hạn số kịch bản lặp lại
    * throughput: Số thông lượng, yêu cầu cần có ramp-up và hold-for
    * steps: Cho phép các người dùng áp dụng các bước của ramp-up cho concurency, yêu cầu cần có ramp-up
    * scenario: Tên của scenario hay còn gọi là tên kịch bản mà được cấu hình trong scenarios

  * **Scenario:**

    Scenario là một chuỗi các bước để xây dựng một bản build script(Ex: Tạo một bản JMX cho JMeter, sử dụng get-requests hay sử dụng file script được viết bằng python để chạy trên grinder)

  * **SoapUI Integration:**

    SoapUI dùng để chỉ định thay cho script, executor sẽ chuyển chúng thành các Taurus scanarios và xử lý với các executor tương ứng

    Ex:

    ```yaml
    scenarios:
      soapui-project:
        script: project.xml
        test-case: TestIndex
    ```

    

  * **Startup Schedule:**

    Thông thường Taurus sẽ chạy các mục được định nghĩa trong execution theo dạng chạy song song(parallel), nếu muốn chuyển sang dạng tuần tự thì có thể thêm option               -sequential, để hạn chế số lượng người thi hành, chúng ta có thể dùng capacity parameter

    Ex:

    ```yaml
    modules:
      local:
        sequential: true # default là false
        capacity: 3 # default là không giới hạn, ở đây nghĩa là không chạy thằng executor thứ 4 cho đến khi có 1 thằng trước đó finished
        # sequential cũng có nghĩa là capacity: 1
    ```

    Nếu muốn chạy các execution khác nhau tại các thời điểm khác nhau thì có thể sử dụng delay hoặc đặt giờ tương ứng

    Ex:

    ```yaml
    execution:
    - concurrency: 10
      hold-for: 20s
      start-at: '2019-03/29 14:30' # chạy tại thời điểm có giờ như này và có time formats là: YYYY-MM-DD HH:MM:SS hoặc YYYY-MM-DD HH:MM hay HH:MM:SS hoặc HH:MM
      scenario: test
    - concurrency: 20
      hold-for: 10s
      scenario: test
      delay: 10s # ở đây sử dụng delay để sau 10s chạy một cái scenario thứ 2 sau 10ss
      
    scenarios:
      test:
        requests:
        -  http://test.com
    ```

    

  * **Additional Files:**

    Khi cái execution yêu cầu thêm file(như JARs, certificates) hoặc gửi tests lên Cloud, cần phải sử dụng files và list paths cho files đó

  * **Environment Variables:**

    Ngoài các biến global environment, thì chúng ta có thể chỉ định trực tiếp môi trường các biến thực thi như:

    ```yaml
    execution:
    - concurrency: 10
      hold-for: 20s
      scenario: test
      env:
        base_url: http://foo.bar/
    ```

    

* **<u>Reporting:</u>**

  Chức năng báo cáo trong Taurus được ủy quyền cho module, chức năng này dùng để theo dõi tổng quan về hệ thống

  Ex:

  ```yaml
  reporting:
  - module: final-stats
  - module: console
  ```

  * **Generating Test Reports:**

    Taurus cung cấp 4 reporter modules:

    * console: cho ra số liệu thống kê trực tiếp từ terminal của người dùng
    * blazemeter: cung cấp các bản test report online
    * final_stats: cung cấp số liệu thống kê một cách tóm tắt sau kiểm tra
    * junit-xml: tạo số liệu thống kê kiểm tra ở định dạng tương thích JUnit

  * **Console Reporter:**

    Console reporter cung cấp một bảng dashboard với các số liệu thống kê kiểm tra trực tiếp và được enable theo mặc định

  * **BlazeMeter Reporter:**

    Blazemeter reporter sẽ tải tất cả các kết quả test lên trang BlazeMeter.com, đây là một nơi cung cấp một giao diện và phân tích các kết quả test, có thể sử dụng nó với -report command

  * **Final Stats Reporter:**

    Dùng để in ra basic console log sau khi chạy test, cái reporter này mặc định đã được bật, có thể tùy chỉnh như ví dụ dưới đây

    Ex:

    ```yaml
    reporting:
    - module: final-stats
      summary: true # Tổng số các sample và % thất bại
      percentiles: true # hiển thị thời gian và phần trăm trung bình
      summary-labels: false # cung cấp danh sách các sample label, status, % of completed, avg time and errors
      failed-labels: false # cung cấp list sample label fail
      test-duration: true # thời gian test
      dump-xml: abc.xml # đặt tên file để export ra 1 file xml
      dump-csv: xyz.csv # đặt tên file để export ra 1 file csv
    ```

    

  * **Dump Summary for Jenkins Plugins:**

    Có 2 options để dump đó là dump-xml và dump-csv cho phép export ra thành các file phục vụ cho nhu cầu sử dụng cho Jenkins Performance Plugin và Jenkins Plot Plugin để show data lên dashboarch. Trong đó csv sử dụng cho plot, còn xml sử dụng cho performance

    Các trường sẽ có trong file dump sẽ bao gồm: label, concurrency, throughput, succ, fail, avg_rt, stdev, avg_ct, avg_lt, rc_200, perc_0.0 => perc_100.0(responce time, 0 là minumum, 100 là maximum), bytes

  * **JUnit XML Reporter:**

    JUnit XML Reporter cung cấp test results ở dạng JUnit XML theo định dạng parseable sử dụng cho Jenkins JUnit Plugin

    Cái reporter này có hai options:

    * filename: path report file, đây là nơi lưu trữ file result

    * data-source: loại data-source để sử dụng, sample-labels hoặc pass-fail. Nếu data-source là sample-labels thì report sẽ chứa các url với các test error, còn nếu data-source là pass-fail thì report sẽ chứa Pass/Fail criteria information. Nhớ rằng cần có pass-fail module trong reporters list, và phải có trước junit-xml module đối với lựa chọn data-source là pass-fail

      Ex:

      ```yaml
      reporting:
      - module: junit-xml
        filename: /root/file.xml # hoặc là /roo/file.csv
        data-source: pass-fail
      ```

      

  * **Results Reading and Aggregating Facility:**

    Không cần quan tâm thằng này nhiều lắm, default của setting thằng aggregating facility là consolidator

  * **Pass/Fail Criteria Subsystem:**

    Pass/Fail module được sử dụng để tự động cập nhật trạng thái theo thời gian đã đặt sẵn.

    Ex:

    ```yaml
    reporting:
    - module: passfail
      criteria:
      - avg-rt of IndexPage>150ms for 10s, stop as failed
      - fail of CheckoutPage>50% for 10s, stop as failed
    ```

    

* **<u>Services:</u>**

  * **Services Subsystem:**

    Khi muốn làm một hành động gì đó có thể là trước, sau hoặc cả trong quá trình test, chúng ta có thể sử dụng subsystem trong Taurus, hiện này subsystem cung cấp 2 services là shellexec và monitoring

    Ex:

    ```yaml
    services:
    - module: shellexec
      post-process: ip a
    - module: monitoring
      server-agent:
      - address: 172.27.100.180:8080
      metrics:
      - cpu
      - memory
      - disks
    ```

    

  * **Shell Executor Service Module:**

    Dùng để thêm các tập lệnh shell vào trong quá trình chạy test

    Ex:

    ```yaml
    services:
    - module: shellexec
      prepare:
      - mkdir test
      writeTest1
      - echo 'test1' >> /tmp/test/log
      writeTest2
      - echo 'test2' >> /tmp/test/log
      post-process:
      - rm -rf /tmp/test/log
    execution:
    - scenario: shelltest
      hold-for: 10s
    scenarios:
      shelltest
      - label: HTTP Request
        method: GET
        url: http://172.27.100.180/
    ```

    

  * **Resource Monitoring Service:**

    Dùng để kiểm tra theo dõi tình trạng của máy chủ, và có thể thu thập thông tin từ các remote servers.

  * **Virtual Display Service:**

    Dùng nếu muốn chạy test bằng cách mở một cửa sổ mới và chạy chúng thì có thể gọi Taurus chạy màn hình ảo bằng cách sử dụng virtual-display service

    Ex:

    ```yaml
    services:
    - module: virtual-display
      width: 1366
      height: 768
    ```

    

  * **Unpacker:**

    Dùng khi muốn unzip một cái file zip nào đó

    Ex:

    ```yaml
    services:
    - module: unpacker
      files:
      - /root/abc.zip
      - c:\abc.zip
    ```

    

  * **Alternate Provisionings and Services:**

    Dùng cho trường hợp muốn chỉ định chạy trên cloud hay local, sẽ có một bài đi sâu rõ hơn về option này

  * **Required Tools Installer:**

    Dùng để kiểm tra  các tools đã cài đặt, hỗ trợ kiểm tra cả blacklist và whitelist như:

    ```shell
    bzt -install-tools -o modules.install-checker.include=jmeter,gatling
    bzt -install-tools -o modules.install-checker.exclude=grinder
    ```

    

  * **Appium Loader:**

    Dùng để start/stop appium server automatically(kiểm thử tự động), cái này cần phải cài thủ công bằng tay, và theo cá nhân mình thì cũng không nên dùng thằng này cho lắm, thằng này chỉ dùng cho mobile web app(hiện tại chưa có nhu cầu)

    Ex:

    ```yaml
    services:
    - appium
    modules:
      appium:
        path: 'path/to/appium/executable'
        timeout: 20 #timeout for start up
    ```

    

  * **Android Emulator Loader:**

    Dùng để test giả lập android(chưa có nhu cầu sử dụng lắm)

    Ex:

    ```yaml
    services:
    - android-emulator
    modules:
      android-emulator:
        oath: /home/Android/sdk/tools/emulator
        avd: android10_arm128
        timeout: 20 # timeout for start up
    ```

    

* **<u>Scenarios:</u>**

  Đây là một cái dictionary của scenario, bên trong là các đặc tả về executions

* **<u>Modules:</u>**

  * **Modules Settings:**

    

* **<u>Settings:</u>**

* **<u>Provisioning:</u>**

* **<u>Included-configs:</u>**







Dưới đây là một ví dụ chứa một vài các section ở phía trên như:

```yaml
execution:
- concurrency: 10
  scenario: sample
  
scenarios:
  sample:
    headers:
        Connection: close
    requests:
    - http://localhost/

reporting:
- module: final-stats
- module: console

modules:
  jmeter:
    path: /opt/jmeter
    properties:
        log_level: DEBUG
  console:
    disable: false
    
settings:
  check-interval: 5s
  default-executor: jmeter

provisioning: local
```

