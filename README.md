## mysql 에서 varchar의 255과 256 차이에 대한 설명
최근 사내에서 한 가지 논의가 이루어졌다. 필자는 varchar의 괄호 안의 숫자는 문자의 길이로만 알고 있었다. 그래서 255와 256 둘 다 길이를 뜻하는 것이기 때문에 실제 저장되는 데이터 바이트 크기는 mysql 내부에서 처리되어 별 차이가 없다고 생각했다.
하지만 차이가 있다는 말을 듣고 해당 부분에 대해 좀 더 상세하게 알아보았다.

[공식 문서](https://dev.mysql.com/doc/refman/8.4/en/char.html)를 확인해보면 varchar 타입을 저장할 때 길이에 따라 1바이트 또는 2바이트 정보를 저장한다고 한다. 해당 바이트를 결정하는 것은 바로 길이인데 255 길이를 넘지 않는다면 1바이트를 넘는다면 2바이트를 사용한다.
> In contrast to CHAR, VARCHAR values are stored as a 1-byte or 2-byte length prefix plus data. The length prefix indicates the number of bytes in the value. A column uses one length byte if values require no more than 255 bytes, two length bytes if values may require more than 255 bytes

즉 내가 알고 있던 괄호 안의 숫자는 길이 정보가 맞았지만 255 이하 길이를 사용하느냐 그 이상으로 사용하느냐에 따라 길이 정보를 저장하는 바이트 수에 차이가 있기 때문에 Mysql 내부 관점에서는 차이가 있는 것이 맞았다.
또한 255와 256의 차이는 charset에 따라 달라질 수 있는데 예를 들어 한 문자당 4바이트를 사용하는 utf8md4 문자집합을 사용하게되면 255가 아닌 VARCHAR(64)부터 2바이트로 길이 정보로 활용하게 된다.

개인적으로 성능 상에 문제가 있는지 확인해보고 싶었지만 커뮤니티에 있는 글들이 너무 상반되는 것들이 많아 정확하게 알 수가 없었다. 다만 바이트 수 저장에 차이가 있다는 내용은 공식문서를 통해 확인이 되었기에 
VARCHAR를 활용할 때에는 문자집합에 따라 설계할 때 길이 바이트 수를 고려하면 더 나은 설계를 할 수 있을 것 같다.
