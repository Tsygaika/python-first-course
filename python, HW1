import asyncio
import aiofiles
import aiohttp
from bs4 import BeautifulSoup


async def get_code(session, url):
    async with session.get(url) as response:    #отправляем запрос для получения кода
        return await response.text()    #возвращаем содержимое ответа в виде текста


async def get_links(html, url):
    soup = BeautifulSoup(html, 'html.parser')   #создаем объект для парсинга сайта
    links = [a.get('href') for a in soup.find_all('a', href = True)]  #и получаем все html теги <a> с атрибутом href,
    for i in range(len(links)):
        if 'http' not in links[i]:#если ссылка относительная, то сделаем ее абсолютной
            links[i] = url + links[i]
            
    return links                                                                                


async def save_to_file(links):
    async with aiofiles.open('links.txt', 'a') as file:    #открываем файл для записи в асинхронном режиме
        for link in links:  #и добавляем ссылки в файл
            await file.write(link + '\n')


async def process_url(session, url):    #функция обработки каждой ссылки
    try:
        html_code = await get_code(session, url)    #получаем html-код сайта
        links = await get_links(html_code, url)  #получаем ссылки из кода
        await save_to_file(links)
        
    except Exception:
        print(f'Не удалось получить доступ к сайту: {url}')
        

async def main(urls):
    async with aiohttp.ClientSession() as session:  #создаем сессию http-клиента
        tasks = [process_url(session, url) for url in urls] #создаем задачи для обрвботки каждой ссылки из urls
        await asyncio.gather(*tasks)


urls = [
    'https://regex101.com/',
    'https://docs.python.org/3/this-url-will-404.html',
    'https://www.nytimes.com/guides/',
    'https://www.mediamatters.org/',
    'https://1.1.1.1/',
    'https://www.politico.com/tipsheets/morning-money',
    'https://www.bloomberg.com/markets/economics',
    'https://www.ietf.org/rfc/rfc2616.txt']

asyncio.run(main(urls))
