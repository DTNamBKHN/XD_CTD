### Y/c 1:
+) Dinh danh ko phan biet hoa thuong
```
Token* readIdentKeyword(void) {
    Token *token = makeToken(TK_NONE, lineNo, colNo);
    int dem = 1;

    token->string[0] = (char)currentChar;
    readChar();

    while ((currentChar != EOF) &&
    ((charCodes[currentChar] == CHAR_LETTER) || (charCodes[currentChar] == CHAR_DIGIT))) {
        if (dem <= MAX_IDENT_LEN)
            token->string[dem++] = (char)currentChar;
        readChar();
    }

    if (dem > MAX_IDENT_LEN) {
        error(ERR_IDENTTOOLONG, token->lineNo, token->colNo);
        return token;
    }

    token->string[dem] = '\0';
    token->tokenType = checkKeyword(token->string);
    while(dem>=0){
        token->string[dem] = tolower(token->string[dem]);
        dem--;
    }
    if (token->tokenType == TK_NONE)
        token->tokenType = TK_IDENT;

    return token;
}
```
+) keyword phai la chu thuong: sửa chỗ khai báo trong ham token.c và xóa hàm upper
### Y/c 2: Xử lý trường hợp định danh cũng không phân biệt chữ hoa, chữ thường
```
Xử lý giống y/c 1
```
### Y/c 3: Nếu yêu cầu thêm một cách viết chú thích, bát đầu bằng  (( cho đến hết dòng thì em xử lý thế nào?
```
them skipComment2() và thêm lệnh vào case CHAR_LPAR
void skipComment2(){
    while (currentChar != EOF && currentChar != '\n')
        readChar();
}

case CHAR_LPAR:
    ln=lineNo;
    cn=colNo;
    readChar();
    if(currentChar==EOF)return makeToken(SB_LPAR,ln,cn);
    if(charCodes[currentChar]==CHAR_PERIOD)
    {
      readChar();
      return makeToken(SB_LSEL,ln,cn);
    }
    else if(charCodes[currentChar]==CHAR_TIMES)
    {
      readChar();
      skipComment();
      return getToken();
    }
    else if(charCodes[currentChar]==CHAR_LPAR)
    {
      readChar();
      skipComment2();
      return getToken();
    }
    else return makeToken(SB_LPAR,ln,cn);
```
### Y/c 4: Kiểm soát số theo đúng giời hạn của unsigned int trong C
```
Token* readNumber(void) {
    Token *token = makeToken(TK_NUMBER, lineNo, colNo);
    int dem = 0;

    while ((currentChar != EOF) && (charCodes[currentChar] == CHAR_DIGIT)) {
        token->string[dem++] = (char)currentChar;
        readChar();
    }
    if (dem <= 9){//neu < 10 ki tu thi auto dung
        token->string[dem] = '\0';
        token->value = atoi(token->string);
        return token;
    }
    else{//neu = 10
        if (strcmp(token->string,"4294967295") <= 0){
            token->string[10] = '\0';
            token->value = atoi(token->string);
            if(dem >= 11){
                error(ERR_IDENTTOOLONG, token->lineNo, token->colNo);
            }
            return token;
        }
        else{
            strncpy(token->string, token->string, 9);
            token->string[9] = '\0';
            token->value = atoi(token->string);
            error(ERR_IDENTTOOLONG, token->lineNo, token->colNo);
            return token;
        }
    }
}
```
### Thay đổi cách viết chú thích: Bao trong cặp {}
```
if (currentChar == '{')
  {
      skipComment3();
      readChar();
      return getToken();
  }
  void skipComment3(){
    while (currentChar != EOF && currentChar != '}')
        readChar();
}
```
### Xử lý trường hợp dấu nháy đơn viết bằng ''''(4 dấu nháy đơn)
```

```
