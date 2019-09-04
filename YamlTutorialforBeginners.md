# Overview about YAML

### YAML là gì

* YAML là một ngôn ngữ đánh dấu(indentation-based markup language) dựa trên các thụt đầu dòng với mục đích vừa dễ đọc vừa dễ viết. Nhiều dự án sử dụng nó vì tính dễ đọc dễ viết, đơn giản và là một ngôn ngữ hỗ trợ cho nhiều ngôn ngữ lập trình
* YAML sử dụng thụt lề để biểu diễn cấu trúc tài liệu(trái ngược với JSON, sử dụng các dấu {} và []). Ngoài ra JSON và YAML rất giống nhau, và chúng có thể chuyển đổi convert lẫn nhau

### YAML Syntax

YAML bao gồm các yếu tố sau:

* Scalars:

  * Scalars gồm các giá trị thông thường như: numbers, strings, booleans:

    ```yaml
    number-value: 100
    string-value: 'hello'
    boolean-value: true
    ```

    

* Lists and Dictionaries

  * Lists là tập hợp các elements

    ```yaml
    lists:
      - sonar
      - jenkins
      - coverity
      - blackduck
    ```

    các element trong lists phải được thụt đầu dòng và có dấu gạch ngang

  * Dictionaries là tập hợp các key, mục đích của nó dùng để ánh xạ giá trị

    ```yaml
    people:
      name: Sandy
      species: human
      height: 1m75
      weight: 70kg
    ```

    Note:

    * bắt buộc sau dấu hai chấm phải có dấu space

    * List có thể lồng trong dictionary và ngược lại

      ```yaml
      requests:
        - http://test.com
        - url: http://http://test.com
          method: get
      ```

    * Có thể sử dụng inline syntax cho list và dictionary như:

      ``` yaml
      lists: [1, 2, 3, 4, 5, 6, 7]
      people: {name: Sandy, species: human, height: 1m75, weight: 70kg}
      ```

### YAML Multi Documents

YAML cho phép nhiều tài liệu được nhúng trong một tệp và phân tách nhau bằng ba dấu gạch ngang:

```yaml
---
document: this is a document 1
---
document: this is a document 2
```

### YAML debugging tips

Có nhiều công cụ có thể sử dụng giúp tìm kiếm các syntax errors như:

* Sử dụng online tool để convert sang JSON để kiểm tra kiến trúc(sử dụng cái này nếu chưa có nhiều kinh nghiệm sử dụng với các ngôn ngữ dựa trên việc thụt lề)
* Sử dụng yamllint để xem có bất kỳ lỗi nào hoặc sự cố hay không

### Một số lưu ý khi viết YAML

* Các ký tự đặc biệt thì phải cho vào trong unquoted strings:

  ```yaml
  unquoted-string: "sử dụng trong trường hợp có các ký tự đặc biệt như là `[]{}:<>|"
  ```

* Không nên sử dụng các tab để thụt lề mặc dù trong một vài trường hợp có thể hợp lệ, đây cũng chính là một nguyên nhân khi code không để ý sử dụng cả tab lẫn thụt đầu dòng dẫn tới file bị lỗi. Thay vào đó chúng ta có thể sử dụng dấu space trong cả quá trình sử dụng.

