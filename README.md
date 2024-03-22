# Reflection - Tutorial 6 ｡⋆୨୧˚ 

1. [**Commit 1 Reflection notes**] Simply, the **handle_connection** method reads HTTP request line by line, stores it for further processing if neeeded, and prints out sentence for debugging.

    ```
    fn handle_connection(mut stream: TcpStream)
    ```
    This function takes ownership of a **TcpStream** object, stream. The mut keyword indicates that stream is mutable, meaning its contents can be modified within the function.
    
    ```
    let buf_reader = BufReader::new(&mut stream);
    ```
    A **BufReader** object named buf_reader is created by calling the new function on BufReader and passing a mutable reference to stream to it. 
    
    ```
    let http_request: Vec<_> = buf_reader
        .lines()
        .map(|result| result.unwrap())
        .take_while(|line| !line.is_empty())
        .collect();
    ```
    **buf_reader.lines()**: This returns an iterator over the lines of the input stream (stream), where each line is returned as a Result containing either the line or an error.
    
    **.map(|result| result.unwrap())**: This maps each Result to the line itself (unwrap is used here to extract the line from the Result). This operation converts the iterator of Results into an iterator of lines.
    
    **.take_while(|line| !line.is_empty())**: This takes lines from the iterator while the line is not empty. Once an empty line is encountered, it stops taking lines.
    
    **.collect()**: This collects the lines into a Vec<_>, where _ is a placeholder for the inferred type. So http_request is a vector containing all the lines of the HTTP request.
    
    ```
    println!("Request: {:#?}", http_request);
    ```
    Lastly, it prints out the HTTP request stored in http_request vector for debugging purposes

2. [**Commit 2 Reflection notes**] Basically, the new handle_connection generates a HTTP response with fixed status line **HTTP/1.1 200 OK** and the content of the response is from **hello.html**.
   
   ```
   let status_line = "HTTP/1.1 200 OK";
   ```
   This line defines a constant **status_line** with the value **HTTP/1.1 200 OK**

    ```
    let contents = fs::read_to_string("hello.html").unwrap();
    ```
    Read the contents of the file named **hello.html** into a string.

    ```
    let length = contents.len();
    ```
    This line calculates the length of the content read from "hello.html" using the len() method of the String type.

    ```
    stream.write_all(response.as_bytes()).unwrap();
    ```
    Finally, it writes the response string to the TCP stream using **write_all**. This function takes a byte slice as input, so we convert the response string to bytes using **as_bytes()**.
    
   <img width="1280" alt="Screenshot 2024-03-22 094105" src="https://github.com/nabiilahputri13/my-first-repo/assets/124870275/31535205-c67a-411d-ab1b-52ce567120b0">
   [Commit 2 screen capture]
   
3. [**Commit 3 Reflection notes**] In the modified **main.rs** in commit 3, **handle_connection** method is validating user request, if the request is for **/** it will return **hello.html**
    ```
    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
    ```
    But if the user sends another request (aka bad) it will shows **404.html**
    ```
    else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
    ```
    Lastly, refactoring is needed because the **if** and **else** blocks have a lot of repetition. We can make separate **if** and **else** lines that assign the values of **status line** and **filename** to variables.
   ```
   fn handle_connection(mut stream: TcpStream) {
    // --snip--

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response =
        format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
   }
   ```
   This way, it is easier to see the difference between the two cases, and it means we have only one place to update the code if we want to change how the file reading and response writing work.

   <img width="1280" alt="Screenshot 2024-03-22 095615" src="https://github.com/nabiilahputri13/my-first-repo/assets/124870275/d51c65b6-1d62-42f8-a5ce-e68011560722">
   [Commit 3 screen capture]
   
4. [**Commit 4 Reflection notes**] The slow request simulation shows that when we open two browser windows: one for http://127.0.0.1:7878/ and the other for http://127.0.0.1:7878/sleep and enter the / URI a few times, as before, it respond quickly. But if we enter /sleep and then load /, we’ll see that / waits until sleep has slept for its full 5 seconds before loading. We need to implement thread pool to avoid requests backing up behind a slow request.
   
5. 
   

