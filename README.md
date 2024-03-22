# Reflection - Tutorial 6

1. Simply the **handle_connection** method reads HTTP request line by line, stores it for further processing if neeeded, and prints out sentence for debugging.

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

2. The new handle_connection
   <img width="1280" alt="Screenshot 2024-03-22 094105" src="https://github.com/nabiilahputri13/my-first-repo/assets/124870275/31535205-c67a-411d-ab1b-52ce567120b0">
   
4. You better do up to the refactoring one, and you need to explain in your reflection notes, how
to split between response and why the refactoring is needed.
   <img width="1280" alt="Screenshot 2024-03-22 095615" src="https://github.com/nabiilahputri13/my-first-repo/assets/124870275/d51c65b6-1d62-42f8-a5ce-e68011560722">
5. slow request sleep2an
6. yg multithread2 itu
