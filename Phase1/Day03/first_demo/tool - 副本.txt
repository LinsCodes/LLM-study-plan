import os
from openai import OpenAI
from dotenv import load_dotenv, find_dotenv

def get_openai_key():
    _ = load_dotenv(find_dotenv())
    return os.environ['OPENAI_API_KEY']

def get_completion(prompt, model="deepseek-ai/DeepSeek-V2.5", stream=True):
    openai_api_key = get_openai_key()
    client = OpenAI(
        base_url='https://api.siliconflow.cn/v1',
        api_key=openai_api_key
    )

    response = client.chat.completions.create(
        model=model,
        messages=[{"role": "user", "content": prompt}],
        stream=stream
    )

    if stream:
        # 返回一个生成器，逐步产生流式输出的内容
        for chunk in response:
            content = chunk.choices[0].delta.content
            if content is not None:
                yield content  # 将内容通过生成器返回
    else:
        return response.choices[0].message.content
    
