---
title: "NFL_historic_odds"
date: 2026-02-23
draft: false
---

This project explores historic NFL odds and outcomes.  

I used a combination of web scraping, data cleaning, and analysis to identify trends and patterns across seasons.  

The goal is to document the full process from data acquisition to visualization, highlighting the decision-making at each step.  

To start, I needed to create a spreadsheet for historical odds. This didn't need to be automated because it is historical data. However, I would like to figure out how to automate the process during the NFL season. For now, I do not need to do that.

It took a few iterations in order to get data that I really wanted to work with. For this project, I knew I wanted the end result to be a Looker Studio Dashboard. I have had some familiarity with this data viz tool and wanted to try and use this project to start pushing myself towards more data analytics goals. In lieu of sharing every iteration of my data exploration, I'll share some brief highlights below
    -Utilized ChatGPT + Claude to find historical data for all NFL games first (Season, Date, Home/Away, Teams, Score) and imported that into a Google Sheet.
    -Searched multiple sites looking for historical betting data (Spread, Over/Under) and utilized AI to write a Google Apps Script that combined the data.
    -Once we had the information in a spreadsheet, it became obvious that the filters within Google Data Studio would not be possible in the way that I wanted with the data that I had. So, I ended up having Claude write a script that split every game into two rows. I'll explain this transformation in more detail with respect to the dashboard.

You can view my spreadsheet below. 

{{< tableau >}}
<div style="width: 100%; max-width: 900px; margin: 0 auto;">
  <iframe width="100%" height="850" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRKzpzMePwq38w_FOCr0gUN801jLABCxa5Or2hTbmNRecLTffWP9XtvZwabpuY0c6V5aCtFpAidmjj3/pubhtml?widget=true&headers=false" frameborder="0"></iframe>
</div>
{{< /tableau >}}

I may explain more about the spreadsheet here. I may not. For now, I'm going to keep going.


## Looker Studio Dashboard

I'd like to point out a couple of considerations for the dashboard before you jump in. I'll also give you some questions that drove the layout of the dashboard.
- The scorecards across the middle of the screen will look messy until Seasons or Weeks have been filtered.
- The dashboard works best with more restrictions, so don't be afraid to be specific with your constraints. They should all work as a union of events.
- As you can see, weeks 21, 22, 23, and 24 are their respective playoff round as seen on the sheet. I'm still basic so this was my best version of keeping everything working as I had hoped.
- Lastly, the team name is accurate to the time period that they were active. So, it's possible, if looking at a franchise's history, that you'd need to select multiple teams. We utilized a shorthand within the data on the spreadsheet which you'd see as nfl_teams.

The questions I sought to answer were things like the following:

- How did home underdogs do in the last month of the season the last five years?
- How often does the over/under hit for a spread above 56?
- How do the Atlanta Falcons do as a road favorite in history? In the last 20 years? In the last 5 years?
- Is there a difference between over/under totals in early weeks as opposed to later weeks in the season?
- What teams consistently perform the best against the spread over the last 2-3 seasons?

It became the most useful looking at a specific scenario. I'll give an example: In Week 4 of the 2025 NFL season, the Pittsburgh Steelers were a home underdog. They have not had a consistent QB since Big Ben retired, they have had the same coach, so other variables that could sway my data weren't really a factor. In home underdog games since 2021, the Steelers were 9-4-1 against the spread. That's a good singular data point to think they'd at least cover the game. You could also investigate them winning outright in these games, their home record in this time, or even their record against the spread in this time. But, we should see that record closer to .500 than anything. So, it seems substantial enough to think about.

{{< tableau >}}
<iframe width="1300px" height="400" src="https://lookerstudio.google.com/embed/reporting/6292eaeb-9869-46e9-bc6c-94cdb6359560/page/1M" frameborder="0" style="border:0" allowfullscreen></iframe>
{{< /tableau >}}

## Conclusion
What started as a silly idea between a few friends about trying to gain an edge on $0.25 bets became a wonderful example of how data can be found, transformed, and analyzed in a relatively simple setting. There were many discoveries along the way, which I plan, in future projects, to document more precisely than I have here. Additionally, I have not given proper credit to sites like pro-football-reference.com, which were instrumental to finding accurate data. While a proper analysis would give findings based on the questions asked, this project serves as a playground for all NFL fans out there. I'd love to see the interesting conclusions you find by sorting by what you think is interesting, just as I did myself. Start with your favorite team and expand your knowledge from there.

I will add a snippet of apps script below as an example. However, the prompting between myself and Claude would be more valuable in reality, since I can read and interpret the script, but I find writing 400 lines purely on brain power to be incomprehensible with the tools that we have at our disposal. I hope you enjoy playing with the data as much as I have.

## Apps Script Example
For all of those pining to know some of the script I had Claude write for me, I'll attach a snippet below. I iterated a few times and I ended up using a few different scripts. This is just the first portion of my script that transformed the raw data into the data that had two rows per game.

```javascript
function transformNFLData() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sourceSheet = ss.getSheetByName('Raw Data') || ss.getActiveSheet();
  const sourceData = sourceSheet.getDataRange().getValues();
  if (sourceData.length < 2) {
    SpreadsheetApp.getUi().alert('No data found or insufficient data in the sheet');
    return;
  }

  const games = [];
  const errors = [];

  for (let i = 1; i < sourceData.length; i++) {
    const row = sourceData[i];
    if (row.every(cell => cell === '' || cell === null || typeof cell === 'undefined')) continue;

    try {
      let date = row[0];
      
      // parse spread & over/under safely
      let spreadRaw = parseFloat(row[9]);
      if (isNaN(spreadRaw)) spreadRaw = 0;
      let ouRaw = parseFloat(row[10]);
      if (isNaN(ouRaw)) ouRaw = 0;

      const game = {
        date: date,
        season: parseInt(row[1]) || 0,
        week: parseInt(row[2]) || 0,
        playoff: row[3] === true || row[3] === 'TRUE' || row[3] === 'true',
        homeTeam: (row[4] || '').toString().trim(),
        homeScore: (isFinite(row[5]) && row[5] !== '') ? Number(row[5]) : parseInt(row[5]) || 0,
        awayScore: (isFinite(row[6]) && row[6] !== '') ? Number(row[6]) : parseInt(row[6]) || 0,
        awayTeam: (row[7] || '').toString().trim(),
        favoriteTeam: (row[8] || '').toString().trim(),
        spread: spreadRaw,
        overUnder: ouRaw
      };

      if (!game.homeTeam || !game.awayTeam) {
        errors.push(`Row ${i + 1}: Missing team names`);
        continue;
      }

      games.push(game);
    } catch (err) {
      errors.push(`Row ${i + 1}: ${err.message}`);
      continue;
    }
  }

  if (games.length === 0) {
    SpreadsheetApp.getUi().alert('No valid games found in the data');
    return;
  }

  const teamRows = transformToTeamRows(games);

  // output sheet creation/clear
  let outputSheet = ss.getSheetByName('Transformed Data');
  if (outputSheet) {
    outputSheet.clear();
  } else {
    outputSheet = ss.insertSheet('Transformed Data');
  }

  const headers = [
    'Date', 'Season', 'Week', 'Playoff', 'Team', 'Opponent', 'Home_Away',
    'Team_Score', 'Opponent_Score', 'Win', 'Point_Margin', 'Favorite',
    'Spread', 'Covered_Spread', 'Over_Under_Line', 'Total_Points', 'Over_Under_Result'
  ];

  const outputData = [headers];
  teamRows.forEach(r => {
    outputData.push([
      r.Date, r.Season, r.Week, r.Playoff, r.Team, r.Opponent,
      r.Home_Away, r.Team_Score, r.Opponent_Score, r.Win, r.Point_Margin,
      r.Favorite, r.Spread, r.ATS_Result, r.Over_Under_Line,
      r.Total_Points, r.Over_Under_Result
    ]);
  });

  outputSheet.getRange(1, 1, outputData.length, headers.length).setValues(outputData);
  outputSheet.getRange(1, 1, 1, headers.length).setFontWeight('bold').setBackground('#4285f4').setFontColor('white');
  outputSheet.autoResizeColumns(1, headers.length);

  const summary = `
Transformation Complete!

📊 Summary:
• ${games.length} games processed
• ${teamRows.length} team records created
• ${errors.length} errors encountered

${errors.length > 0 ? '⚠️ Errors:\n' + errors.slice(0, 10).join('\n') : ''}
  `.trim();

  SpreadsheetApp.getUi().alert(summary);
  outputSheet.activate();
}

```