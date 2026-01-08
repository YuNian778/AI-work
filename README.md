import gradio as gr
import requests

API_KEY = "sk-daedc90412104d1599a75c113bd787f1"

def generate_travel_plan(destination, days, preference):
    url = "https://dashscope.aliyuncs.com/api/v1/services/aigc/text-generation/generation"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {API_KEY}"
    }
    prompt = f"""请生成{days}天的{destination}旅行行程，偏好{preference}：
1. 每日行程（时间+地点+玩法）
2. 当地美食推荐
3. 2个旅行注意事项"""
    data = {
        "model": "qwen-turbo",
        "input": {"prompt": prompt},
        "parameters": {"max_tokens": 500}
    }
    response = requests.post(url, json=data, headers=headers)
    return response.json()["output"]["text"]

with gr.Blocks() as demo:
    gr.Markdown("### AI旅游规划师")
    destination = gr.Textbox(label="目的地", placeholder="如：成都")
    days = gr.Textbox(label="天数", placeholder="如：2")
    preference = gr.Textbox(label="偏好", placeholder="如：美食")
    output = gr.Textbox(label="行程", lines=10)
    gr.Button("生成").click(generate_travel_plan, [destination, days, preference], output)

if __name__ == "__main__":
    demo.launch()
