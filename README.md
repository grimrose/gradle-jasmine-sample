Gradle PhantomJS Jasmine Sample
------------------------------------

Gradleを利用してPhantomJS + Jasmineのテストを実行できるサンプルです。

## 準備

まず、GradleからPhantomJSを使えるようにします。

既にPhantomJSを別Directoryにて設定している場合は、以下のように *build.gradle*に設定することで利用できるようになります。		

```groovy
obake {
	phantomjsBinaryPath = 'path/to/phantomjs-binary-directory'
}
```

また、以下のタスクを実行するとダウンロードされて、使えるようになります。	

```bash
$ gradle preparePhantomJS
```	

次に、以下のコマンドを実行します。

```bash
$ gradle phantomJSAgent
```

結果として、以下のように表示されれば、PhantomJSは利用可能です。

```bash
using PhantomJS version 1.9.2

BUILD SUCCESSFUL
```

## Jasmine Reporter

今回は、jasmine-standaloneのSpecRunner.htmlを利用します。
このプロジェクトでは、以下の場所に設置してあります。

```groovy
"${projectDir}/jasmine-standalone-1.3.1/SpecRunner.html"
```

今回は、コンソールから分かりやすいようにTAP形式で出力してみます。
以下のコードをSpecRunner.htmlに追加します。

```javascript
jasmineEnv.addReporter(new jasmine.TapReporter());
```

次に、以下のようにタスクを設定します。

```groovy
task runJasmine(type: org.bitbucket.grimrose.gradle.PhantomJSAgentTask) {
    js = 'path-to-phantomjs-examples-dir/run-jasmine.js'
    args = [file("$projectDir/jasmine-standalone-1.3.1/SpecRunner.html")]
}
```

次に、以下のコマンドを実行します。

```bash
$ gradle runJasmine
```

実行結果は、以下のように出力されます。

```bash
:runJasmine
1..5
ok 1 - Player : should be able to play a Song
ok 2 - when song has been paused : should indicate that the song is currently paused
ok 3 - when song has been paused : should be possible to resume
ok 4 - Player : tells the current song if the user has made it a favorite
ok 5 - #resume : should throw an exception if song is already playing
# 5 specs, 8 assertions, 0 failures in 0.017s.
'waitFor()' finished in 200ms.

Player
Passing 5 specs


BUILD SUCCESSFUL

Total time: 7.819 secs
```

その他のReporterも同じように追加することで、利用できるようになります。

## JUnit形式のxml出力

Jenkinsと連携する際は、TAP形式でも出来ますが、JUnit形式のxmlでもやってみましょう。

以下のコードをSpecRunner.htmlに追加します。

*"${buildDir}/test-js-results"* にxmlファイルを出力して欲しいので、 **JUnitXmlReporter**の第一引数に渡します。

```javascript
jasmineEnv.addReporter(new jasmine.JUnitXmlReporter('build/test-js-results'));
```

次に、以下のようにタスクを設定します。

```groovy
task runJasmineXml(type: org.bitbucket.grimrose.gradle.PhantomJSAgentTask) {
    js = 'path-to-jasmine-reporters/phantomjs-testrunner.js'
    args = [file("$projectDir/jasmine-standalone-1.3.1/SpecRunner.html")]
}
```

次に、以下のコマンドを実行します。

```bash
$ gradle runJasmineXml
```

実行されると、xmlファイルが出力されます。
