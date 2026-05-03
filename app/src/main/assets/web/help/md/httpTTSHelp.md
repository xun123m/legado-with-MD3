# 在线朗读规则说明

* 在线朗读规则为url规则,同书源url
* js参数
```
speakText //朗读文本
speakSpeed //朗读速度,5-50
```
* 音频提取规则
```
audioRule // 可选。TTS返回JSON或文本时, 用JSONPath提取base64音频, 或用@js:脚本从result中返回base64音频
```
* 例:
```
http://tts.baidu.com/text2audio,{
    "method": "POST",
    "body": "tex={{java.encodeURI(java.encodeURI(speakText))}}&spd={{String((speakSpeed + 5) / 10 + 4)}}&per=5003&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=1&vol=5&pit=5&_res_tag_=audio"
}
```
* 大模型TTS例: Xiaomi MiMo-V2.5-TTS
```
url:
https://api.xiaomimimo.com/v1/chat/completions,{
    "method": "POST",
    "body": {{(function(){
        var info = source.getLoginInfoMap()
        var model = info.get('Model') || 'mimo-v2.5-tts'
        var audio = {format: 'wav'}
        // VoiceDesign模型按官方示例不传voice; 普通TTS模型才传voice.
        if (String(model).toLowerCase().indexOf('voicedesign') < 0) {
            audio.voice = info.get('Voice') || 'mimo_default'
        }
        var messages = []
        messages.push({role: 'user', content: info.get('Style') || '以自然、清晰、适合长篇小说朗读的语气朗读。'})
        messages.push({role: 'assistant', content: speakText})
        var body = {}
        body.model = model
        body.messages = messages
        body.audio = audio
        return JSON.stringify(body)
    })()}}
}

Content-Type:
application/json

音频提取规则:
$.choices[0].message.audio.data

请求头:
@js:var info = source.getLoginInfoMap(); JSON.stringify({"api-key": info.get('API Key') || "", "Content-Type": "application/json"})
```
