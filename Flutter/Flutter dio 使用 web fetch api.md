Flutter dio 使用 web fetch api 在Web环境中流式聊天

浏览器运行参数

```
--web-browser-flag "--disable-web-security" --web-hostname 127.0.0.1 --web-port 13001
```

![image-20231110031526438](https://raw.githubusercontent.com/wilinz/picgo/main/image/image-20231110031526438.png)

![image-20231110031605071](https://raw.githubusercontent.com/wilinz/picgo/main/image/imageimage-20231110031605071.png)

```yaml
# pubspec.yaml
environment:
  sdk: '>=3.1.0 <4.0.0'
  
dependencies:
  # ...
  dio: ^5.3.3
  fetch_client: ^1.0.2
  native_dio_adapter: ^1.1.0
```

```dart
  Future<void> _chat() async {
    // 创建一个新的 Dio 实例用于发起 HTTP 请求。
    final dio = Dio();

    // 设置 HTTP 客户端适配器为 Fetch 。
    dio.httpClientAdapter = ConversionLayerAdapter(FetchClient(mode: RequestMode.cors));

    // 设置请求头。
    final headers = {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer $openAIAPIKey', // 用你实际的 API 密钥替换 openAIAPIKey。
    };

    // 创建包含聊天 API 所需参数的请求体。
    final requestBody = {
      'model': 'gpt-3.5-turbo', // 指定用于聊天的模型。
      'messages': [
        {'role': 'system', 'content': 'You are a helpful assistant.'}, // 系统消息
        {'role': 'user', 'content': '写1000字故事'}, // 用户消息，请求一个包含1000个字符的故事
      ],
      'stream': true, // 启用响应流
    };

    // 发起 HTTP POST 请求到聊天 API 的端点。
    final response = await dio.post<ResponseBody>(
      'https://api.f2gpt.com/v1/chat/completions', // 聊天完成的 API 端点
      data: requestBody, // 设置请求体
      options: Options(
        responseType: ResponseType.stream, // 将响应类型设置为流
        headers: headers, // 设置请求头
      ),
    );

    // 订阅响应流并处理接收到的数据。
    response.data?.stream
        .transform(unit8Transformer) // 将流数据转换为 Uint8List
        .transform(const Utf8Decoder()) // 将流数据解码为 UTF-8
        .transform(const LineSplitter()) // 将流数据拆分为行
        .listen((chunk) {
      print(chunk); // 输出响应流的每个数据块
    }, onError: (error) {
      // 处理流处理过程中出现的任何错误。
    });
  }


  StreamTransformer<Uint8List, List<int>> unit8Transformer =
      StreamTransformer.fromHandlers(
    handleData: (data, sink) {
      sink.add(List<int>.from(data));
    },
  );
```

## 以下是 ConversionLayerAdapter 的实现，从 native_dio_adapter 拷贝的，因为它没有公开此类，因此我只能复制粘贴

```dart
import 'dart:async';
import 'dart:convert';
import 'dart:typed_data';

import 'package:dio/dio.dart';
import 'package:http/http.dart';

/// A conversion layer which translates Dio HTTP requests to
/// [http](https://pub.dev/packages/http) compatible requests.
/// This way there's no need to implement custom [HttpClientAdapter]
/// for each platform. Therefore, the required effort to add tests is kept
/// to a minimum. Since `CupertinoClient` and `CronetClient` depend anyway on
/// `http` this also doesn't add any additional dependency.
class ConversionLayerAdapter implements HttpClientAdapter {
  final Client client;

  ConversionLayerAdapter(this.client);

  @override
  Future<ResponseBody> fetch(
    RequestOptions options,
    Stream<Uint8List>? requestStream,
    Future<dynamic>? cancelFuture,
  ) async {
    final request = await _fromOptionsAndStream(options, requestStream);
    final response = await client.send(request);
    return response.toDioResponseBody();
  }

  @override
  void close({bool force = false}) => client.close();

  Future<BaseRequest> _fromOptionsAndStream(
    RequestOptions options,
    Stream<Uint8List>? requestStream,
  ) async {
    final request = Request(
      options.method,
      options.uri,
    );

    request.headers.addAll(
      Map.fromEntries(
        options.headers.entries.map((e) => MapEntry(e.key, e.value.toString())),
      ),
    );

    request.followRedirects = options.followRedirects;
    request.maxRedirects = options.maxRedirects;

    if (requestStream != null) {
      final completer = Completer<Uint8List>();
      final sink = ByteConversionSink.withCallback(
        (bytes) => completer.complete(Uint8List.fromList(bytes)),
      );
      requestStream.listen(
        sink.add,
        onError: completer.completeError,
        onDone: sink.close,
        cancelOnError: true,
      );
      final bytes = await completer.future;
      request.bodyBytes = bytes;
    }
    return request;
  }
}

extension on StreamedResponse {
  ResponseBody toDioResponseBody() {
    final dioHeaders = headers.entries.map((e) => MapEntry(e.key, [e.value]));
    return ResponseBody(
      stream.cast<Uint8List>(),
      statusCode,
      headers: Map.fromEntries(dioHeaders),
      isRedirect: isRedirect,
      statusMessage: reasonPhrase,
    );
  }
}
```



## 效果

![image-20231110031740415](https://raw.githubusercontent.com/wilinz/picgo/main/image/image-20231110031740415.png)