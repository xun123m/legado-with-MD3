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
    "body": {
        "model": {{JSON.stringify(source.getLoginInfoMap().get('Model') || 'mimo-v2.5-tts')}},
        "messages": [
            {
                "role": "user",
                "content": {{JSON.stringify(source.getLoginInfoMap().get('Style') || '以自然、清晰、适合长篇小说朗读的语气朗读。')}}
            },
            {
                "role": "assistant",
                "content": {{JSON.stringify(speakText)}}
            }
        ],
        "audio": {
            "format": "wav",
            "voice": {{JSON.stringify(source.getLoginInfoMap().get('Voice') || 'mimo_default')}}
        }
    }
}

Content-Type:
application/json

音频提取规则:
$.choices[0].message.audio.data

请求头:
@js:var info = source.getLoginInfoMap(); JSON.stringify({"api-key": info.get('API Key') || "", "Content-Type": "application/json"})
```
