import requests
from bs4 import BeautifulSoup


URL = "https://example.com/registered-projects"


def fetch_project_details(url):
    """
    Fetch project details such as GSTIN No, PAN No, Name, Permanent Address from the detail page.
    """
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')


    gstin = soup.find('div', {'class': 'gstin'}).get_text(strip=True)
    pan = soup.find('div', {'class': 'pan'}).get_text(strip=True)
    name = soup.find('div', {'class': 'name'}).get_text(strip=True)
    address = soup.find('div', {'class': 'address'}).get_text(strip=True)

    return {
        'GSTIN No': gstin,
        'PAN No': pan,
        'Name': name,
        'Permanent Address': address
    }


def scrape_registered_projects(url, max_projects=6):
    """
    Scrape the first `max_projects` under the "Registered Projects" heading from the given URL.
    """
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    projects = []
    project_links = soup.find_all('a', {'class': 'project-link'}, limit=max_projects)

    for link in project_links:
        detail_url = link['href']
        project_details = fetch_project_details(detail_url)
        projects.append(project_details)

    return projects


if __name__ == "__main__":
    project_list = scrape_registered_projects(URL)
    for project in project_list:
        print(project)
