# FileSystem
nodejs file system


## 1. read file

- readFileSync : 동기로 파일 읽기
    ```javaScript
    fs.readFileSync('./package.json', 'utf8');
    ```

- writeFileSync : 동기로 파일 쓰기
    ```javaScript
    fs.writeFileSync('./text.txt', 'Hello World', function(err){
        if(err){
            console.log(err);
        }
    });
    ```

- readFile : 비동기로 파일 읽기
    ```javaScript
    fs.readFile('./package.json', 'utf8', function(err, data){
        console.log(data);
    });
    ```

- writeFile : 비동기로 파일 쓰기
    ```javaScript
    fs.writeFile('./text.txt', 'Hello world!');    
    ```

- 적용
    ```javaScript
    var server = http.createServer();

    server.on('request', (req, res)=>{
        // 파일 비동기로 읽기
        fs.readFile('backpack.png', (err, data)=>{
            if(err) throw err;
            res.writeHead(200, {'Content-Type':'image/png'});
            res.write(data);
            res.end('end');
        });
        // 스트림 열고 파이프 연결
        var fileIs = fs.createReadStream('backpack.png', {flags:'r'});
        fileIs.pipe(res);
    });
    ```


## 2. stream file

### (1) stream 입출력1

    스트림을 통해서 데이터 입출력

- A. 파일 디렉토리 만들고 삭제하기
    ```javaScript
    fs.mkdir('./docs', (err)=>{
        if(err) throw err;
        console.log('새로운 폴더 생성');
        
        fs.rmdir('./docs', (err)=>{
            console.log('docs 폴더를 삭제했습니다');
        })
    })
    ```


- B. 스트림 생성
    ```javaScript
    var infile = fs.createReadStream('./text.txt', {flags:'r'});
    var outfile = fs.createWriteStream('./text2.txt', {flags:'w'});
    ```


- C. data 읽어오기
    ```javaScript
    infile.on('data', (data)=>{
        console.log('읽어들인 데이터 : '+data);
        outfile.write(data);
    })
    ```


- D. 스트림 닫기
    ```javaScript
    infile.on('end', ()=>{
        console.log('파일 읽기 종료');
        outfile.end(()=>{
            console.log('파일 쓰기 종료');
        });
    });
    ```

### (2) stream 입출력2

    스트림에 파이프를 이용해서 입출력

- A. 파일 존재 확인

    ```javaScript
    var inName = './text.txt';
    var outName = './text2.txt';

    fs.exists(outName, (exists)=>{
        if(exists){
            fs.unlink(outName, (err)=>{
                if(err) console.log(err);
            })
            console.log('기존 파일 삭제');
        }
        스트림 생성 후
        파이프로 입출력
    })
    ```

- B. 스트림 생성

    ```javaScript   
    var infile = fs.createReadStream(inName, {flags:'r'});
    var outfile = fs.createWriteStream(outName, {flags:'w'});
    ```

- C. 파이프로 입출력

    ```javaScript
    infile.pipe(outfile);
    infile.on('end', ()=>{
        console.log('읽기 완료');
        outfile.end(()=>{
            console.log('복사 완료');
        })
    })
    ```


### (3) buffer 입출력

- 파일 쓰기
    ```javaScript
    fs.open('./text.txt', 'w', (err, fd)=>{
        if(err){
            console.log(err);
        }
        console.dir(fd);
        var buffer = new Buffer('안녕!\n');
        // 2. 쓰기
        fs.write(fd, buffer, 0, buffer.length, null, (err, written, buffer)=>{
            if(err){
                console.log(err);
            }
            console.log(err, written, buffer);
            // 3. 닫기
            fs.close(fd, ()=>{
                console.log('파일 열고 닫기 완료');
            });
        });
    });
    ```

- 파일 읽기
    ```javaScript
    fs.open('./text.txt', 'r', (err, fd)=>{
        if(err){
            console.log(err);
        }
        var buffer = new Buffer('뭐야');
        // 2. 쓰기
        fs.read(fd, buffer, 0, buffer.length, null, (err, bytesRead, buffer)=>{
            if(err){
                console.log(err);
            }
            var str = buffer.toString('utf8', 0, bytesRead);
            console.log(str);
            console.log(err, bytesRead, buffer);
            // 3. 닫기
            fs.close(fd, (err)=>{
                console.log('읽기 완료');
            });
        });
    });
    ```
