{
  "nbformat": 4,
  "nbformat_minor": 0,
  "metadata": {
    "colab": {
      "provenance": [],
      "authorship_tag": "ABX9TyMJTxFt4xJUolBIiQHVqY7I",
      "include_colab_link": true
    },
    "kernelspec": {
      "name": "python3",
      "display_name": "Python 3"
    },
    "language_info": {
      "name": "python"
    }
  },
  "cells": [
    {
      "cell_type": "markdown",
      "metadata": {
        "id": "view-in-github",
        "colab_type": "text"
      },
      "source": [
        "<a href=\"https://colab.research.google.com/github/steffenvogler/biomedical-courses/blob/main/chat_L0_chapter.ipynb\" target=\"_parent\"><img src=\"https://colab.research.google.com/assets/colab-badge.svg\" alt=\"Open In Colab\"/></a>"
      ]
    },
    {
      "cell_type": "code",
      "execution_count": null,
      "metadata": {
        "id": "uSttNVWt3pY7"
      },
      "outputs": [],
      "source": [
        "#chat chapter"
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "!pip install --upgrade pip\n",
        "!pip install requests\n",
        "!pip install openai"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "VFxxesMIRcJm",
        "outputId": "2efe1202-f3e4-47b1-8ac8-f339ccda7831"
      },
      "execution_count": 50,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Requirement already satisfied: pip in /usr/local/lib/python3.11/dist-packages (25.1.1)\n",
            "Requirement already satisfied: requests in /usr/local/lib/python3.11/dist-packages (2.32.3)\n",
            "Requirement already satisfied: charset-normalizer<4,>=2 in /usr/local/lib/python3.11/dist-packages (from requests) (3.4.2)\n",
            "Requirement already satisfied: idna<4,>=2.5 in /usr/local/lib/python3.11/dist-packages (from requests) (3.10)\n",
            "Requirement already satisfied: urllib3<3,>=1.21.1 in /usr/local/lib/python3.11/dist-packages (from requests) (2.4.0)\n",
            "Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.11/dist-packages (from requests) (2025.6.15)\n",
            "Requirement already satisfied: openai in /usr/local/lib/python3.11/dist-packages (1.93.0)\n",
            "Requirement already satisfied: anyio<5,>=3.5.0 in /usr/local/lib/python3.11/dist-packages (from openai) (4.9.0)\n",
            "Requirement already satisfied: distro<2,>=1.7.0 in /usr/local/lib/python3.11/dist-packages (from openai) (1.9.0)\n",
            "Requirement already satisfied: httpx<1,>=0.23.0 in /usr/local/lib/python3.11/dist-packages (from openai) (0.28.1)\n",
            "Requirement already satisfied: jiter<1,>=0.4.0 in /usr/local/lib/python3.11/dist-packages (from openai) (0.10.0)\n",
            "Requirement already satisfied: pydantic<3,>=1.9.0 in /usr/local/lib/python3.11/dist-packages (from openai) (2.11.7)\n",
            "Requirement already satisfied: sniffio in /usr/local/lib/python3.11/dist-packages (from openai) (1.3.1)\n",
            "Requirement already satisfied: tqdm>4 in /usr/local/lib/python3.11/dist-packages (from openai) (4.67.1)\n",
            "Requirement already satisfied: typing-extensions<5,>=4.11 in /usr/local/lib/python3.11/dist-packages (from openai) (4.14.0)\n",
            "Requirement already satisfied: idna>=2.8 in /usr/local/lib/python3.11/dist-packages (from anyio<5,>=3.5.0->openai) (3.10)\n",
            "Requirement already satisfied: certifi in /usr/local/lib/python3.11/dist-packages (from httpx<1,>=0.23.0->openai) (2025.6.15)\n",
            "Requirement already satisfied: httpcore==1.* in /usr/local/lib/python3.11/dist-packages (from httpx<1,>=0.23.0->openai) (1.0.9)\n",
            "Requirement already satisfied: h11>=0.16 in /usr/local/lib/python3.11/dist-packages (from httpcore==1.*->httpx<1,>=0.23.0->openai) (0.16.0)\n",
            "Requirement already satisfied: annotated-types>=0.6.0 in /usr/local/lib/python3.11/dist-packages (from pydantic<3,>=1.9.0->openai) (0.7.0)\n",
            "Requirement already satisfied: pydantic-core==2.33.2 in /usr/local/lib/python3.11/dist-packages (from pydantic<3,>=1.9.0->openai) (2.33.2)\n",
            "Requirement already satisfied: typing-inspection>=0.4.0 in /usr/local/lib/python3.11/dist-packages (from pydantic<3,>=1.9.0->openai) (0.4.1)\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "# Make sure to restart Colab runtime after installing dependencies\n",
        "import os\n",
        "try:\n",
        "    import google.colab\n",
        "    os._exit(0)\n",
        "except ImportError:\n",
        "    pass"
      ],
      "metadata": {
        "id": "GxYV4OHWRqwO"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "import os, requests, io\n",
        "from openai import OpenAI"
      ],
      "metadata": {
        "id": "1q165MfGLDoZ"
      },
      "execution_count": null,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "from google.colab import userdata\n",
        "api_key = userdata.get(\"CHAT_ACTIVELOOP_TOKEN\")\n",
        "\n",
        "assert api_key is not None, \"API key not found!\""
      ],
      "metadata": {
        "id": "lJFI159YCvSE"
      },
      "execution_count": 3,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "response = requests.get(\n",
        "    \"https://api.activeloop.ai/files\",\n",
        "    headers={\"Authorization\": f\"Bearer {api_key}\", \"Accept\": \"*/*\"}\n",
        ")\n",
        "\n",
        "count = 1\n",
        "if response.status_code == 200:\n",
        "    files_data = response.json()\n",
        "    files = files_data.get('data', [])\n",
        "\n",
        "\n",
        "    for file in files:\n",
        "        file_id = file.get('id')\n",
        "        if file_id:\n",
        "            delete_response = requests.delete(\n",
        "                f\"https://api.activeloop.ai/files/{file_id}\",\n",
        "                headers={\"Authorization\": f\"Bearer {api_key}\", \"Accept\": \"*/*\"}\n",
        "            )\n",
        "            if delete_response.status_code == 200:\n",
        "                print(f\"Deleted file with ID: {file_id} {count}\")\n",
        "                count += 1\n",
        "            else:\n",
        "                print(f\"Failed to delete file with ID: {file_id}. Status code: {delete_response.status_code}\")\n",
        "        else:\n",
        "            print(\"File without 'id' found, skipping.\")\n",
        "else:\n",
        "    print(f\"Failed to list files. Status code: {response.status_code}\")\n"
      ],
      "metadata": {
        "colab": {
          "base_uri": "https://localhost:8080/"
        },
        "id": "X87Su14hXA-B",
        "outputId": "727fc74d-e98e-41a6-e78d-67117a2f3f56"
      },
      "execution_count": 4,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Deleted file with ID: a1ae0d7dbfff4325af4acc04556d3607 1\n",
            "Deleted file with ID: 71d3cbedc4de4d149230509d2f049ab0 2\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "pdf_urls = [\"https://pmc.ncbi.nlm.nih.gov/articles/PMC10185891/pdf/fphar-14-1096366.pdf\",\n",
        "            \"https://pmc.ncbi.nlm.nih.gov/articles/PMC6297296/pdf/clinmed-16-5-481.pdf\",\n",
        "            \"https://bmchealthservres.biomedcentral.com/counter/pdf/10.1186/s12913-019-4651-7.pdf\",\n",
        "            \"https://pmc.ncbi.nlm.nih.gov/articles/PMC8149349/pdf/41999_2021_Article_481.pdf\",\n",
        "            \"https://pmc.ncbi.nlm.nih.gov/articles/PMC1113033/pdf/1295.pdf\",\n",
        "            \"https://pmc.ncbi.nlm.nih.gov/articles/PMC3679367/pdf/nihms-463926.pdf\"]\n",
        "\n",
        "\n",
        "files = [('file', (os.path.basename(url), io.BytesIO(requests.get(url).content))) for url in pdf_urls]\n",
        "\n",
        "response = requests.post(\n",
        "      'https://api.activeloop.ai/files',\n",
        "      headers={\"Authorization\": f\"Bearer {api_key}\"},\n",
        "      files=files\n",
        ")\n",
        "# Once uploaded, it would take few minutes to index"
      ],
      "metadata": {
        "id": "iGLq5BziTaTk"
      },
      "execution_count": 5,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "response = requests.get(\n",
        "    \"https://api.activeloop.ai/files\",\n",
        "    headers={\"Authorization\": f\"Bearer {api_key}\",\"Accept\":\"*/*\"},\n",
        ")\n",
        "\n",
        "data = response.json()"
      ],
      "metadata": {
        "id": "gv6Is8ZOBLx6"
      },
      "execution_count": 13,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "for file_info in data['data']:\n",
        "    print(f\"Filename: {file_info['filename']}\")\n",
        "    print(f\"Status: {file_info['status']}\")"
      ],
      "metadata": {
        "id": "dy7YVA40BzBj",
        "outputId": "9fa38d95-8391-4f28-864c-3bdef8f0347b",
        "colab": {
          "base_uri": "https://localhost:8080/"
        }
      },
      "execution_count": 14,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Filename: fphar-14-1096366.pdf\n",
            "Status: indexed\n",
            "Filename: clinmed-16-5-481.pdf\n",
            "Status: indexed\n",
            "Filename: s12913-019-4651-7.pdf\n",
            "Status: indexed\n",
            "Filename: 41999_2021_Article_481.pdf\n",
            "Status: indexed\n",
            "Filename: 1295.pdf\n",
            "Status: indexed\n",
            "Filename: nihms-463926.pdf\n",
            "Status: indexed\n"
          ]
        }
      ]
    },
    {
      "cell_type": "code",
      "source": [
        "client = OpenAI(\n",
        "    base_url=\"https://api.activeloop.ai/\",\n",
        "    api_key=api_key\n",
        ")\n",
        "\n",
        "response = client.chat.completions.create(\n",
        "    model=\"activeloop-l0\",\n",
        "    messages=[\n",
        "        {\n",
        "            \"role\": \"user\",\n",
        "            \"content\": \"Per the various articles on Adverse Drug Effects, explain some common adverse drug effects,\" +\n",
        "                       \"and further elaborate on the prevelance of these adverse drug effects in different demographic groups.\" +\n",
        "                        \"Also, explain notable adverse drug-drug interactions\"\n",
        "        }\n",
        "    ],\n",
        "    stream=True\n",
        ")\n",
        "\n",
        "chunks = [chunk.choices[0] for chunk in response]\n",
        "thinking = \"\".join([c.delta.reasoning_content for c in chunks if c.delta.reasoning_content is not None])\n",
        "answer = \"\".join([c.delta.content for c in chunks if c.delta.content is not None])\n",
        "citations = chunks[-1].metadata['relevant_docs']"
      ],
      "metadata": {
        "id": "tz4znnyu4NkX"
      },
      "execution_count": 15,
      "outputs": []
    },
    {
      "cell_type": "code",
      "source": [
        "print(answer)"
      ],
      "metadata": {
        "id": "vktlFJIVD6oP",
        "outputId": "20186c41-da68-4346-b9da-93a05c0906db",
        "colab": {
          "base_uri": "https://localhost:8080/"
        }
      },
      "execution_count": 16,
      "outputs": [
        {
          "output_type": "stream",
          "name": "stdout",
          "text": [
            "Common Adverse Drug Reactions\n",
            "![Overview of Adverse Drug Events](4bd70b)\n",
            "The table provides an overview of adverse drug events (ADEs) from several studies, detailing types of events, associated drugs, and prevalence.\n",
            "\n",
            "1.  Central nervous system (CNS) effects\n",
            "    *   Fatigue (55.3%), dizziness (18.4%), tremor (15.8%) [e412_8].\n",
            "    ![Adverse Drug Events and Associated Drugs](025bd5)\n",
            "    The table provides information on adverse drug events, including types of events, associated drugs, incidence/prevalence, causes, and consequences. For example, Theitler et al. [40] found that drug-related fatigue (55.3%), dizziness (18.4%), and tremor (15.8%) were common adverse events.\n",
            "    *   More broadly, CNS events accounted for 33% of all ADRs in one ambulatory-care study [e412_5].\n",
            "2.  Gastrointestinal (GI) effects\n",
            "    *   Nausea, vomiting, diarrhea—reported across multiple primary-care settings [e412_8].\n",
            "    *   GI disorders comprised 22% of ADRs in ambulatory care [e412_5].\n",
            "3.  Cardiovascular effects\n",
            "    *   Haemorrhagic events (20%), hypotension and other cardiac disorders [e412_5].\n",
            "    *   Cardiovascular ADRs made up 18% of events in primary care [e412_5].\n",
            "\n",
            "Prevalence in Different Populations\n",
            "![Adverse Drug Events Prevalence](463658)\n",
            "Table 2 provides information on adverse drug events (ADEs) from several studies, including their rates and prevalence in different populations.\n",
            "*   Overall burden\n",
            "    *   ADRs account for ~3.5% of hospital admissions and ~197,000 deaths per year in Europe [e412_1].\n",
            "*   Ambulatory-care patients\n",
            "    *   27 ADRs per 100 patients over study period [e412_5].\n",
            "*   Long-term care residents\n",
            "    *   9.8 ADEs per 100 resident-months; 42% judged preventable [e412_5].\n",
            "*   General adult population\n",
            "    *   12.0% experienced an ADE over a 3-month window (95%\n"
          ]
        }
      ]
    }
  ]
}