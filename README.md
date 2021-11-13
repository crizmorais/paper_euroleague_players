# Supplementary material

All codes in this Markdown were made using JupyterLab, and executed cell by cell.

To make the scripts we use the programming language Python. The follow librarys helped us to get the job done:

* requests
* BeautifulSoup
* pandas

For each competition (Adidas Next Generation, Euroleague and Eurocup), data from different pages in the respective website are scraped, considering the year of the competition and the information we want. To do that we need to search in two different pages for every year, for each competition, because the 'minutes_played' and 'index', were in different pages. Before gathering all the data, we made a third script, to delete duplicated information. 

## 1. Adidas Next Generation

All data came from: (https://www.adidasngt.com/). 

### 1.1. Index 

    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::4]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season
    
    base_url = 'https://www.adidasngt.com/'
    stats = f'{base_url}u18/game-center/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=Valuation&agg=Accumulated' for season in season_dict.values()]
    
    lista_rank = []
    lista_player = []
    lista_team = []
    lista_games = []
    lista_index = []
    lista_aver = []
    lista_per = []
    lista_season = []

    for url in link_seasons:
        season_pg = get(url)
        season_page = bs(season_pg.text, 'html.parser')
        if season_page.find('span', {'class': 'wp-pager-pages'}):
            last_page = int(get_last_page(url))
            last_url = ['{}&page={}'.format(url, n) for n in range(1, last_page+1)]
            for urls in last_url:
                pagina = get(urls)
                ler_pg = bs(pagina.text, 'html.parser')
                lista_rank += get_rank(urls)
                lista_player += get_player(urls)
                lista_team += get_team(urls)
                lista_games += get_games(urls)
                lista_index += get_index(urls)
                lista_aver += get_aver(urls)
                lista_per += get_per(urls)
                lista_season += [get_season(urls)] * len(get_rank(urls))
    print('done!')
    
    stats_dict = {'season':lista_season, 'rank':lista_rank, 'player':lista_player, 'team':lista_team, 'games':lista_games, 'index':lista_index, 'average':lista_aver, 'per_40min':lista_per}
    
    df = pd.DataFrame(stats_dict)
    df.to_csv('PlayersU18.csv')
    
____
### 1.2. Minutes played


    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::2]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_minutes(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        minutes = [n.get_text()[0:] for n in games_tag[1::2]]
        return minutes

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season

    base_url = 'https://www.adidasngt.com/'
    stats = f'{base_url}u18/game-center/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=TimePlayed&agg=Accumulated' for season in season_dict.values()]
    
    lista_rank = []
    lista_player = []
    lista_team = []
    lista_games = []
    lista_season = []
    lista_minutes = []

    for url in link_seasons:
        season_pg = get(url)
        season_page = bs(season_pg.text, 'html.parser')
        if season_page.find('span', {'class': 'wp-pager-pages'}):
            last_page = int(get_last_page(url))
            last_url = ['{}&page={}'.format(url, n) for n in range(1, last_page+1)]
            for urls in last_url:
                pagina = get(urls)
                ler_pg = bs(pagina.text, 'html.parser')
                lista_rank += get_rank(urls)
                lista_player += get_player(urls)
                lista_team += get_team(urls)
                lista_games += get_games(urls)
                lista_minutes += get_minutes(urls)
                lista_season += [get_season(urls)] * len(get_rank(urls))
    print('done!')
____
### 1.3. Delete duplicates

    import pandas as pd
    
    df = pd.read_csv('PlayersU18.csv')
    df1 = pd.read_csv('U18_minutes.csv')
    
    playersIndex = df.drop('Unnamed: 0', axis=1)
    playersIndex.columns = playersIndex.columns.str.strip()
    playersMinutes = df1.drop('Unnamed: 0', axis=1)
    playersMinutes.columns = playersMinutes.columns.str.strip()
    
    #merge databases
    baseU18 = playersIndex.merge(playersMinutes[['season', 'player', 'team', 'games', 'minutes']],'left',on=['season', 'player', 'team', 'games'],suffixes=('', 'minutes'))
    
    dup_index = baseU18.groupby(['player'])['index'].transform(max) == baseU18['index']
    base_index = baseU18[dup_index].sort_values(by=['player'])
    dup_avg = base_index.groupby(['player'])['average'].transform(max) == base_index['average']
    base = base_index[dup_avg].sort_values(by=['player'])
    
    base.to_csv('baseU18.csv')
    
____

## 2. Euroleague

All data came from: (https://www.euroleague.net/).

### 2.1. Index

    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::4]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season
        
    base_url = 'https://www.euroleague.net/'
    stats = f'{base_url}main/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=Valuation&agg=Accumulated' for season in season_dict.values()]
    
    lista_rank = []
    lista_player = []
    lista_team = []
    lista_games = []
    lista_index = []
    lista_aver = []
    lista_per = []
    lista_season = []

    for url in link_seasons:
        season_pg = get(url)
        season_page = bs(season_pg.text, 'html.parser')
        if season_page.find('span', {'class': 'wp-pager-pages'}):
            last_page = int(get_last_page(url))
            last_url = ['{}&page={}'.format(url, n) for n in range(1, last_page+1)]
            for urls in last_url:
                pagina = get(urls)
                ler_pg = bs(pagina.text, 'html.parser')
                lista_rank += get_rank(urls)
                lista_player += get_player(urls)
                lista_team += get_team(urls)
                lista_games += get_games(urls)
                lista_index += get_index(urls)
                lista_aver += get_aver(urls)
                lista_per += get_per(urls)
                lista_season += [get_season(urls)] * len(get_rank(urls))
    print('done!')
    
    stats_dict = {'season':lista_season, 'rank':lista_rank, 'player':lista_player, 'team':lista_team, 'games':lista_games, 'index':lista_index, 'average':lista_aver, 'per_40min':lista_per}

    df = pd.DataFrame(stats_dict)
    df.to_csv('Players_Euroleague.csv')
_________    
### 2.2. Minutes Played
    
    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::2]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_minutes(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        minutes = [n.get_text()[0:] for n in games_tag[1::2]]
        return minutes

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season

    base_url = 'https://www.euroleague.net/'
    stats = f'{base_url}main/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=TimePlayed&agg=Accumulated' for season in season_dict.values()]
    
    lista_rank = []
    lista_player = []
    lista_team = []
    lista_games = []
    lista_season = []
    lista_minutes = []

    for url in link_seasons:
        season_pg = get(url)
        season_page = bs(season_pg.text, 'html.parser')
        if season_page.find('span', {'class': 'wp-pager-pages'}):
            last_page = int(get_last_page(url))
            last_url = ['{}&page={}'.format(url, n) for n in range(1, last_page+1)]
            for urls in last_url:
                pagina = get(urls)
                ler_pg = bs(pagina.text, 'html.parser')
                lista_rank += get_rank(urls)
                lista_player += get_player(urls)
                lista_team += get_team(urls)
                lista_games += get_games(urls)
                lista_minutes += get_minutes(urls)
                lista_season += [get_season(urls)] * len(get_rank(urls))
    print('ACABOU!')

    stats_dict = {'season':lista_season, 'rank':lista_rank, 'player':lista_player, 'team':lista_team, 'games':lista_games, 'minutes':lista_minutes}

    df = pd.DataFrame(stats_dict)
    df.to_csv('Euroleague_minutes.csv')
_____
### 2.3. Delete duplicates

    import pandas as pd
    
    df = pd.read_csv('Players_Euroleague.csv')
    df1 = pd.read_csv('Euroleague_minutes.csv')
    
    playersIndex = df.drop('Unnamed: 0', axis=1)
    playersIndex.columns = playersIndex.columns.str.strip()
    playersMinutes = df1.drop('Unnamed: 0', axis=1)
    playersMinutes.columns = playersMinutes.columns.str.strip()
    
    baseU18 = playersIndex.merge(playersMinutes[['season', 'player', 'team', 'games', 'minutes']],'left',on=['season', 'player', 'team', 'games'],suffixes=('', 'minutes'))
    
    dup_index = baseU18.groupby(['player'])['index'].transform(max) == baseU18['index']
    base_index = baseU18[dup_index].sort_values(by=['player'])
    
    dup_avg = base_index.groupby(['player'])['average'].transform(max) == base_index['average']
    base = base_index[dup_avg].sort_values(by=['player'])
    
    base.to_csv('baseEuroleague.csv')
___
## 3. Eurocup

All data came from: (https://www.eurocupbasketball.com/)

### 3.1. Index

    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::4]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season
        
    base_url = 'https://www.eurocupbasketball.com/'
    stats = f'{base_url}eurocup/games/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=Valuation&agg=Accumulated' for season in season_dict.values()]

    lista_rank = []
    lista_player = []
    lista_team = []
    lista_games = []
    lista_index = []
    lista_aver = []
    lista_per = []
    lista_season = []

    for url in link_seasons:
        season_pg = get(url)
        season_page = bs(season_pg.text, 'html.parser')
        if season_page.find('span', {'class': 'wp-pager-pages'}):
            last_page = int(get_last_page(url))
            last_url = ['{}&page={}'.format(url, n) for n in range(1, last_page+1)]
            for urls in last_url:
                pagina = get(urls)
                ler_pg = bs(pagina.text, 'html.parser')
                lista_rank += get_rank(urls)
                lista_player += get_player(urls)
                lista_team += get_team(urls)
                lista_games += get_games(urls)
                lista_index += get_index(urls)
                lista_aver += get_aver(urls)
                lista_per += get_per(urls)
                lista_season += [get_season(urls)] * len(get_rank(urls))
    print('done!')
    
    stats_dict = {'season':lista_season, 'rank':lista_rank, 'player':lista_player, 'team':lista_team, 'games':lista_games, 'index':lista_index, 'average':lista_aver, 'per_40min':lista_per}

    df = pd.DataFrame(stats_dict)
    df.to_csv('index_eurocup.csv')
___
### 3.2. Minutes Played

    from requests import get
    from bs4 import BeautifulSoup as bs
    import pandas as pd
    import csv
    from time import sleep

    def get_last_page(url: str) -> str:
        pages = get(url)
        pages_lida = bs(pages.text, 'html.parser')
        links = pages_lida.find('span', {'class': 'wp-pager-pages'}).find_all('a')
        return max([link.get_text() for link in links])

    def get_rank(url):
        rank_tag = ler_pg.find_all('td', {'class':'IndividualStatsRank'})
        ranks = [rank.get_text() for rank in rank_tag]
        return ranks

    def get_player(url):
        player_tag = ler_pg.find_all('a', {'class':'StatsCenterPlayerName'})
        players = [nome.find('span', {'class':'hidden-xs'}).get_text() for nome in player_tag]
        return players

    def get_team(url):
        team_tag = ler_pg.find_all('td', {'class':'StatsCenterTeams'})
        team = []
        for n in team_tag:
            if n.find('span', {'class':'visible-sm-inline'}) != None:
                team.append(n.find('span', {'class':'visible-md-inline'}).get_text())
            else:
                team.append('N/A')
        #team = [n.find('span', {'class':'visible-sm-inline'}).get_text() for n in team_tag]
        return team

    def get_games(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        games = [n.get_text()[0:] for n in games_tag[0::2]]
        return games

    def get_index(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        index_r = [n.get_text()[0:] for n in games_tag[1::4]]
        return index_r

    def get_aver(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        aver = [n.get_text()[0:] for n in games_tag[2::4]]
        return aver

    def get_per(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        per_40 = [n.get_text()[0:] for n in games_tag[3::4]]
        return per_40

    def get_minutes(url):
        tabela = ler_pg.find('table', {'class':'StatsGridResults table responsive fixed-cols-2 table-left-cols-2 table-expand table-striped table-hover table-noborder table-centered table-condensed'})
        games_tag = tabela.find_all('td', {'align':'right'})
        minutes = [n.get_text()[0:] for n in games_tag[1::2]]
        return minutes

    def get_season(url):
        season = ler_pg.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find('option', {'selected':'selected'}).get_text()
        return season
        
    base_url = 'https://www.eurocupbasketball.com/'
    stats = f'{base_url}eurocup/games/statistics'

    stats_get = get(stats)
    stats_page = bs(stats_get.text, 'html.parser')
    
    seasons = stats_page.find('select', {'name':'ctl00$ctl00$ctl00$ctl00$maincontainer$maincontent$contentpane$ctl01$ddlSeasonSingle'}).find_all('option')

    season_dict = {season.get_text() : season.get('value')[11:] for season in seasons}

    link_seasons = [f'{stats}?mode=Leaders&entity=Players&seasonmode=Single&seasoncode={season}&cat=TimePlayed&agg=Accumulated' for season in season_dict.values()]
    
    stats_dict = {'season':lista_season, 'rank':lista_rank, 'player':lista_player, 'team':lista_team, 'games':lista_games, 'minutes':lista_minutes}

    df = pd.DataFrame(stats_dict)
    df.to_csv('minutes_eurocup.csv')
___
### 3.3. Delete duplicates

    import pandas as pd
    
    df = pd.read_csv('index_eurocup.csv')
    df1 = pd.read_csv('minutes_eurocup.csv')
    
    playersIndex = df.drop('Unnamed: 0', axis=1)
    playersIndex.columns = playersIndex.columns.str.strip()
    playersMinutes = df1.drop('Unnamed: 0', axis=1)
    playersMinutes.columns = playersMinutes.columns.str.strip()
    
    baseU18 = playersIndex.merge(playersMinutes[['season', 'player', 'team', 'games', 'minutes']],'left',on=['season', 'player', 'team', 'games'],suffixes=('', 'minutes'))
    
    dup_index = baseU18.groupby(['player'])['index'].transform(max) == baseU18['index']
    base_index = baseU18[dup_index].sort_values(by=['player'])

    dup_avg = base_index.groupby(['player'])['average'].transform(max) == base_index['average']
    base = base_index[dup_avg].sort_values(by=['player'])
    
    base.to_csv('baseEurocup.csv')
___
## 4. Gathering data

To gather all data in a single CSV file, we needed to made a fourth script. But, different from the other, in this case we needed to explore the data to fill all the gaps. To a better undestand of the process you can access the Jupyter Notebook available in the link below.

https://github.com/crizmorais/paper_euroleague_players/blob/main/merge_bases.ipynb

## 5. Disclaimer

There are probably several ways to do the same process proposed above, faster and even more efficiently. Even so, we believe that sharing the work done can serve as a starting point for those who want to do something similar.
