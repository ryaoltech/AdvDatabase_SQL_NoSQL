--TABLE COUNTRY
CREATE SEQUENCE prj_seq_country
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_Country
  ( idCountry number default prj_seq_country.NEXTVAL PRIMARY KEY,
    CountryName varchar2(10) );

--TABLE CITY
CREATE SEQUENCE prj_seq_city
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_City
  ( idCity number default prj_seq_city.NEXTVAL PRIMARY KEY,
    idCountry number,
    CityName varchar2(20)
     );

ALTER TABLE prj_tbl_City
ADD CONSTRAINT fk_country FOREIGN KEY (idCountry)
REFERENCES prj_tbl_Country (idCountry)
ON DELETE CASCADE;
     
     

--TABLE PLAYER
CREATE SEQUENCE prj_seq_player
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_Player
  ( idPlayer number default prj_seq_player.NEXTVAL PRIMARY KEY,
    idCity number,
    FirstName varchar2(20),
    LastName varchar2(20),
    GamesWon number,
    PhoneNumber varchar2(15),
    CONSTRAINT fk_city FOREIGN KEY (idCity)
        REFERENCES prj_tbl_City (idCity)
        ON DELETE CASCADE
     );

--TABLE GAMESTATUS
CREATE SEQUENCE prj_seq_gamestatus
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_GameStatus
  ( idStatus number default prj_seq_gamestatus.NEXTVAL PRIMARY KEY,
    StatusName varchar2(20) );

--TABLE KNOCKOUTSTAGE
CREATE SEQUENCE prj_seq_knockoutstage
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_KnockoutStage
  ( idKnockoutStage number default prj_seq_knockoutstage.NEXTVAL PRIMARY KEY,
    StageName varchar2(20) );

--TABLE GAME
CREATE SEQUENCE prj_seq_game
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_Game
  ( idGame number default prj_seq_game.NEXTVAL PRIMARY KEY,
    GameName varchar2(20),
    Version varchar2(20) );

--TABLE TOURNAMENT
CREATE SEQUENCE prj_seq_tournament
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;

CREATE TABLE prj_tbl_Tournament
  ( idTournament number default prj_seq_tournament.NEXTVAL PRIMARY KEY,
    idGame number,
    idCity number,
    TournamentName varchar2(20),
    StartDate TIMESTAMP,
    EndDate TIMESTAMP,
    idKnockoutStage number,
    champion number,
    CONSTRAINT fk_tournament_city FOREIGN KEY (idCity)
        REFERENCES prj_tbl_City (idCity)
        ON DELETE CASCADE,
    CONSTRAINT fk_tournament_game FOREIGN KEY (idGame)
        REFERENCES prj_tbl_Game (idGame)
        ON DELETE CASCADE,
    CONSTRAINT fk_tournament_knockoutstage FOREIGN KEY (idKnockoutStage)
        REFERENCES prj_tbl_KnockoutStage (idKnockoutStage)
        ON DELETE CASCADE

     );
     

--TABLE MATCH
CREATE SEQUENCE prj_seq_match
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;


CREATE TABLE prj_tbl_Match
  ( idMatch number default prj_seq_match.NEXTVAL PRIMARY KEY,
    idTournament number,
    idPlayer1 number,
    idPlayer2 number,
    idKnockoutStage number,
    GameDate TIMESTAMP,
    Score varchar2(10),
    idStatus number,
    WinningPlayer number,
    NGroup number,
    CONSTRAINT fk_match_tournament FOREIGN KEY (idTournament)
        REFERENCES prj_tbl_Tournament (idTournament)
        ON DELETE CASCADE,
    CONSTRAINT fk_match_player1 FOREIGN KEY (idPlayer1)
        REFERENCES prj_tbl_Player (idPlayer)
        ON DELETE CASCADE,
    CONSTRAINT fk_match_player2 FOREIGN KEY (idPlayer2)
        REFERENCES prj_tbl_Player (idPlayer)
        ON DELETE CASCADE,
    CONSTRAINT fk_match_knockoutstage FOREIGN KEY (idKnockoutStage)
        REFERENCES prj_tbl_KnockoutStage (idKnockoutStage)
        ON DELETE CASCADE,
    CONSTRAINT fk_match_status FOREIGN KEY (idStatus)
        REFERENCES prj_tbl_GameStatus (idStatus)
        ON DELETE CASCADE,
    CONSTRAINT fk_match_winningplayer FOREIGN KEY (WinningPlayer)
        REFERENCES prj_tbl_Player (idPlayer)
        ON DELETE CASCADE
     );
     
CREATE TABLE prj_tbl_PlayersEnrolled
  ( 
    idTournament number,
    idPlayer number,
    CONSTRAINT fk_playersEnrolled_tournament FOREIGN KEY (idTournament)
        REFERENCES prj_tbl_Tournament (idTournament)
        ON DELETE CASCADE,
    CONSTRAINT fk_playersEnrolled_idplayer FOREIGN KEY (idPlayer) 
        REFERENCES prj_tbl_Player (idPlayer)
        ON DELETE CASCADE
     );

ALTER TABLE prj_tbl_PlayersEnrolled ADD CONSTRAINT playersEnrolled_fk_combination UNIQUE (idTournament, idPlayer);

CREATE INDEX idx_match_tournament
ON PRJ_TBL_MATCH(IDTOURNAMENT, IDKNOCKOUTSTAGE, NGROUP);

CREATE INDEX idx_player_id
ON PRJ_TBL_PLAYER(IDPLAYER);


INSERT INTO prj_tbl_Country (CountryName) VALUES ('Canada');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('USA');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('England');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Germany');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('France');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Japan');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Brazil');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Australia');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Chile');
INSERT INTO prj_tbl_Country (CountryName) VALUES ('Peru');


INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (1, 'Ottawa');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (1, 'Toronto');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (1, 'Vancouver');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (1, 'Calgary');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (2, 'Washington, D.C.');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (2, 'Miami');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (2, 'Orlando');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (2, 'New York');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (3, 'London');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (4, 'Berlin');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (5, 'Paris');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (6, 'Tokyo');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (7, 'Brasilia');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (8, 'Sidney');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (9, 'Santiago');
INSERT INTO prj_tbl_City (idCountry, CityName) VALUES (10, 'Lima');

INSERT INTO prj_tbl_GameStatus (StatusName) VALUES ('Played');
INSERT INTO prj_tbl_GameStatus (StatusName) VALUES ('Scheduled');
INSERT INTO prj_tbl_GameStatus (StatusName) VALUES ('Cancelled');
INSERT INTO prj_tbl_GameStatus (StatusName) VALUES ('Playing');

INSERT INTO prj_tbl_KnockoutStage (StageName) VALUES ('Eighth finals');
INSERT INTO prj_tbl_KnockoutStage (StageName) VALUES ('Quarter finals');
INSERT INTO prj_tbl_KnockoutStage (StageName) VALUES ('Semi finals');
INSERT INTO prj_tbl_KnockoutStage (StageName) VALUES ('Final');

INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('Call of Duty', 'Black Ops 4');
INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('Counter-Strike', 'Global Offensive');
INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('Dota', '2');
INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('Fortnite', '');
INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('PUBG', '');
INSERT INTO prj_tbl_Game (GameName, Version) VALUES ('FIFA', '23');

commit;

create or replace package tournament_pkg is
    CURSOR cur_knockoutstages is
        select a.idknockoutstage, a.stagename, b.idknockoutstage next_idknockoutstage 
        from prj_tbl_knockoutstage a left join prj_tbl_knockoutstage b on b.idknockoutstage = (a.idknockoutstage + 1);

    PROCEDURE create_player(firstname varchar2, lastname varchar2,city varchar2, phonenumber varchar2 );
    
    PROCEDURE create_tournament(game varchar2,
                                            city varchar2,
                                            tournamentname prj_tbl_tournament.tournamentname%type,
                                            startdate prj_tbl_tournament.startdate%type,
                                            enddate prj_tbl_tournament.enddate%type,
                                            knockoutstage varchar2
                                            

    );
    
    PROCEDURE create_matches(p_idtournament prj_tbl_tournament.idtournament%type);
    
    FUNCTION get_cities(country prj_tbl_country.countryname%type) RETURN SYS_REFCURSOR;
    
    PROCEDURE enroll_players(idtournament prj_tbl_tournament.idtournament%type, idplayer prj_tbl_player.idplayer%type);
    
    FUNCTION getNextGameDate(p_idtournament prj_tbl_tournament.idtournament%type,p_gamedate prj_tbl_tournament.startdate%type) return prj_tbl_tournament.startdate%type;
    
    PROCEDURE update_score(p_idmatch prj_tbl_match.idmatch%type, p_score prj_tbl_match.score%type, p_winningplayer prj_tbl_match.winningplayer%type);
    
    PROCEDURE create_matches_nextPhase (p_idmatch prj_tbl_match.idmatch%type, p_winningplayer prj_tbl_match.winningplayer%type );

end tournament_pkg;


create or replace package body tournament_pkg is

    PROCEDURE create_player(firstname varchar2, lastname varchar2, city varchar2, phonenumber varchar2 )
    is 
    lv_idcity prj_tbl_city.idcity%type;
    begin
        select idcity into lv_idcity from prj_tbl_city where cityname = city;
        
        INSERT INTO prj_tbl_player (idcity, firstname, lastname, gameswon, phonenumber) VALUES (lv_idcity, firstname, lastname,0, phonenumber);
        commit;
    end;

    PROCEDURE create_tournament(game varchar2,
                                            city varchar2,
                                            tournamentname prj_tbl_tournament.tournamentname%type,
                                            startdate prj_tbl_tournament.startdate%type,
                                            enddate prj_tbl_tournament.enddate%type,
                                            knockoutstage varchar2
                                            

    ) is
    lv_idcity prj_tbl_city.idcity%type;
    lv_idgame prj_tbl_game.idgame%type;
    lv_idknockoutstage prj_tbl_knockoutstage.idknockoutstage%type;
    begin
        select idknockoutstage into lv_idknockoutstage from prj_tbl_knockoutstage where stagename = knockoutstage;
        select idgame into lv_idgame from prj_tbl_game where gamename = game;
        select idcity into lv_idcity from prj_tbl_city where cityname = city;
        
        --DBMS_OUTPUT.PUT_LINE('THE IDs for knockoutstage, idgame and idcity are:: '|| lv_idknockoutstage ||' ' || lv_idgame ||' '|| lv_idcity ||' ');
        
        INSERT INTO prj_tbl_tournament (idgame, idcity, tournamentname, startdate, enddate, idknockoutstage, champion) VALUES (lv_idgame, lv_idcity, tournamentname, startdate, enddate, lv_idknockoutstage, null);
        commit;
        DBMS_OUTPUT.PUT_LINE('Tournament Created!');
    end;
    
    
    FUNCTION get_cities(country prj_tbl_country.countryname%type) RETURN SYS_REFCURSOR is
    lv_idcountry prj_tbl_country.idcountry%type;
    v_cursor SYS_REFCURSOR;
    begin
        select idcountry into lv_idcountry from prj_tbl_country where countryname = country;
        
        open v_cursor for
            select cityname from prj_tbl_city where idcountry = lv_idcountry;
        
        return v_cursor;
        
    end get_cities;
    
    PROCEDURE enroll_players(idtournament prj_tbl_tournament.idtournament%type, idplayer prj_tbl_player.idplayer%type) is
    begin
        insert into prj_tbl_playersenrolled(idtournament, idplayer) values (idtournament, idplayer);
    end;
    
    PROCEDURE create_matches(p_idtournament prj_tbl_tournament.idtournament%type) is
    lv_rowcount NUMBER := 0;
    lv_i NUMBER :=0;
    lv_status_match prj_tbl_gamestatus.idstatus%type;
    lv_temp_idmatch prj_tbl_match.idmatch%type;
    lv_number_days NUMBER := 0;
    lv_numberPhases NUMBER := 0;
    lv_idknockoutstage prj_tbl_tournament.idknockoutstage%type;
    lv_nameknockoutstage prj_tbl_knockoutstage.stagename%type;
    lv_gamedate prj_tbl_tournament.startdate%type;
    lv_ngroups number := 0;
    lv_startdate prj_tbl_tournament.startdate%type;
    lv_enddate prj_tbl_tournament.enddate%type;
    cursor cur_players is 
            select idplayer, idknockoutstage from prj_tbl_playersenrolled tp, prj_tbl_tournament tt where tp.idtournament = p_idtournament and tp.idtournament = tt.idtournament ORDER BY DBMS_RANDOM.VALUE;
    begin
        --getting the initial status for a scheduled game.
        select idstatus into lv_status_match from prj_tbl_gamestatus where statusname = 'Scheduled';
        --getting the number of days between each match
        select enddate, startdate, idknockoutstage into lv_enddate, lv_startdate, lv_idknockoutstage from prj_tbl_tournament where idtournament = p_idtournament;
        lv_number_days := EXTRACT(DAY FROM lv_enddate - lv_startdate);
        
        select stagename into lv_nameknockoutstage from prj_tbl_knockoutstage where idknockoutstage = lv_idknockoutstage;
        
        if (lv_nameknockoutstage = 'Eighth finals') then
            lv_number_days := FLOOR(lv_number_days/4);
        elsif (lv_nameknockoutstage = 'Quarter finals') then
            lv_number_days := FLOOR(lv_number_days/3);
        elsif (lv_nameknockoutstage = 'Semi finals') then
            lv_number_days := FLOOR(lv_number_days/2);
        end if;
        
        lv_gamedate := lv_startdate + NUMTODSINTERVAL(lv_number_days, 'DAY');
        --DBMS_OUTPUT.PUT_LINE('lv_gamedate='||lv_gamedate||', lv_startdate='||lv_startdate||', lv_number_days='||lv_number_days||', NumToDSInterval='||NUMTODSINTERVAL(lv_number_days, 'DAY'));
        for row_playersenrolled in cur_players loop
            lv_rowcount := lv_rowcount + 1;
        end loop;
    
        
        if (MOD(lv_rowcount,2) = 0) then
            for player in cur_players loop
                if (MOD(lv_i,2)=0) then
                    lv_temp_idmatch := prj_seq_match.NEXTVAL;
                     lv_ngroups := lv_ngroups + 1;
                    insert into prj_tbl_match (idmatch,idtournament, idplayer1, idplayer2, idKnockoutstage, gamedate, score, idstatus, winningplayer, ngroup) 
                      values(lv_temp_idmatch, p_idtournament, player.idplayer, null, player.idknockoutstage, lv_gamedate, null,lv_status_match, null, lv_ngroups );
                else
                    update prj_tbl_match set idplayer2 = player.idplayer where idmatch = lv_temp_idmatch;
                end if;
                lv_i:=lv_i + 1;
            end loop;
            
        end if;
        delete from prj_tbl_playersenrolled where idtournament = p_idtournament;
        commit;
        DBMS_OUTPUT.PUT_LINE('Matches created!');
    end;
    
    FUNCTION getNextGameDate(p_idtournament prj_tbl_tournament.idtournament%type,p_gamedate prj_tbl_tournament.startdate%type) return prj_tbl_tournament.startdate%type is
    lv_startdate prj_tbl_tournament.startdate%type;
    lv_enddate prj_tbl_tournament.enddate%type;
    lv_idknockoutstage prj_tbl_knockoutstage.idknockoutstage%type;
    lv_nameknockoutstage prj_tbl_knockoutstage.stagename%type;
    lv_number_days NUMBER:=0;
    lv_gamedate prj_tbl_tournament.startdate%type;
    begin
        select startdate, enddate, idknockoutstage into lv_startdate, lv_enddate, lv_idknockoutstage from prj_tbl_tournament where idtournament = p_idtournament;
        --DBMS_OUTPUT.PUT_LINE('THE STARTDATE, ENDDATE AND IDKNOCKOUTSTAGE ARE::'||lv_startdate||' '||lv_enddate||' '||lv_idknockoutstage);
        select stagename into lv_nameknockoutstage from prj_tbl_knockoutstage where idknockoutstage = lv_idknockoutstage;
        --DBMS_OUTPUT.PUT_LINE('THE stage name is::'||lv_nameknockoutstage);
        lv_number_days := EXTRACT(DAY FROM lv_enddate - lv_startdate);
        if (lv_nameknockoutstage = 'Eighth finals') then
            lv_number_days := FLOOR(lv_number_days/4);
        elsif (lv_nameknockoutstage = 'Quarter finals') then
            lv_number_days := FLOOR(lv_number_days/3);
        elsif (lv_nameknockoutstage = 'Semi finals') then
            lv_number_days := FLOOR(lv_number_days/2);
        end if;
         --DBMS_OUTPUT.PUT_LINE('THE number of days is::'||lv_number_days);
        lv_gamedate := p_gamedate + NUMTODSINTERVAL(lv_number_days, 'DAY');
        return lv_gamedate;
        
    EXCEPTION 
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('NO ROWS SELECTED!');
            return null;
    end;
    
    PROCEDURE update_score(p_idmatch prj_tbl_match.idmatch%type, p_score prj_tbl_match.score%type, p_winningplayer prj_tbl_match.winningplayer%type) is
    lv_idstatus prj_tbl_gamestatus.idstatus%type;
    lv_idplayer1 prj_tbl_match.idplayer1%type;
    lv_idplayer2 prj_tbl_match.idplayer2%type;
    ex_value_idplayer EXCEPTION;
    PRAGMA EXCEPTION_INIT(ex_value_idplayer, -20001);
    
    begin
        select idstatus into lv_idstatus from prj_tbl_gamestatus where statusname = 'Played';
        select idplayer1, idplayer2 into lv_idplayer1, lv_idplayer2 from prj_tbl_match where idmatch = p_idmatch;
        
        if not (lv_idplayer1 = p_winningplayer or lv_idplayer2 = p_winningplayer) then
            RAISE ex_value_idplayer;
        end if;
        
        update prj_tbl_match set score = p_score, winningplayer = p_winningplayer, idstatus = lv_idstatus where idmatch = p_idmatch;
        create_matches_nextPhase(p_idmatch,p_winningplayer);
        commit;
        DBMS_OUTPUT.PUT_LINE('Score updated!');
    exception
        when ex_value_idplayer then
            DBMS_OUTPUT.PUT_LINE('The WINNER IDPLAYER should be in IDPLAYER1 OR IDPLAYER2! '|| SQLERRM);
    
    end;
    
    PROCEDURE create_matches_nextPhase (p_idmatch prj_tbl_match.idmatch%type, p_winningplayer prj_tbl_match.winningplayer%type ) is
    lv_status_match prj_tbl_match.idstatus%type;
    lv_new_group prj_tbl_match.ngroup%type;
    lv_curr_group prj_tbl_match.ngroup%type;
    lv_curr_idknockoutstage prj_tbl_knockoutstage.idknockoutstage%type;
    lv_next_idknockoutstage prj_tbl_knockoutstage.idknockoutstage%type;
    lv_idtournament prj_tbl_match.idtournament%type;
    lv_gamedate prj_tbl_match.gamedate%type;
    lv_exist NUMBER:=0;
    lv_isfinal_knockoutstage boolean:=false;
    ex_value_champion EXCEPTION;
    begin
        
    DBMS_OUTPUT.PUT_LINE('EXECUTING THE PROCEDURE NEXT PHASE');
    
        select idtournament, idknockoutstage, ngroup, gamedate into lv_idtournament, lv_curr_idknockoutstage, lv_curr_group, lv_gamedate from prj_tbl_match where idmatch = p_idmatch; 
        
        for knockout_row in tournament_pkg.cur_knockoutstages loop
            if knockout_row.idknockoutstage = lv_curr_idknockoutstage then 
                lv_next_idknockoutstage := knockout_row.next_idknockoutstage;
                if knockout_row.stagename = 'Final' then
                    lv_isfinal_knockoutstage := true;
                end if;
            end if;
        end loop;
        
        if (lv_curr_group = 1) or (lv_curr_group =2) then lv_new_group :=1;
        elsif (lv_curr_group = 3) or (lv_curr_group =4) then lv_new_group :=2;
        elsif (lv_curr_group = 5) or (lv_curr_group =6) then lv_new_group :=3;
        elsif (lv_curr_group = 7) or (lv_curr_group =8) then lv_new_group :=4;
        end if;
        --DBMS_OUTPUT.PUT_LINE('Beginning to evaluate next stage. New group would be:: '||lv_new_group||' and next stage will be:: '||lv_next_idknockoutstage);
        
        select idstatus into lv_status_match from prj_tbl_gamestatus where statusname = 'Scheduled';
        --DBMS_OUTPUT.PUT_LINE('THE VALUE OF lv_status_match IS:: '||lv_status_match);
        
        
        
        select count(*) into lv_exist from prj_tbl_match where idtournament=lv_idtournament and idknockoutstage = lv_next_idknockoutstage and ngroup = lv_new_group;    
        --DBMS_OUTPUT.PUT_LINE('THE VALUE OF LV_EXIST IS:: '||lv_exist);
        if lv_isfinal_knockoutstage then
            --DBMS_OUTPUT.PUT_LINE('BEFORE THROWING THE EXCEPTION');
            RAISE ex_value_champion;
            
        else
            if (lv_exist = 0) then
                --DBMS_OUTPUT.PUT_LINE('The record doesnt exist, so we are gonna insert');
                if (MOD(lv_curr_group,2) = 1) then
                   -- DBMS_OUTPUT.PUT_LINE('We are gonna do:: insert into prj_tbl_match (idmatch, idtournament, idplayer1, idplayer2, idknockoutstage, gamedate, score, idstatus, winningplayer, ngroup) values ('|| prj_seq_match.CURRVAL ||','||
                    --lv_idtournament||','|| p_winningplayer||', null,'|| lv_next_idknockoutstage||','|| tournament_pkg.getNextGameDate(lv_idtournament, lv_gamedate)||', null,'||lv_status_match||', null,'|| lv_new_group||' );'
                    --);
                    insert into prj_tbl_match (idtournament, idplayer1, idplayer2, idknockoutstage, gamedate, score, idstatus, winningplayer, ngroup) 
                        values(lv_idtournament, p_winningplayer, null, lv_next_idknockoutstage, tournament_pkg.getNextGameDate(lv_idtournament, lv_gamedate), null,lv_status_match, null, lv_new_group );
                else 
                    insert into prj_tbl_match (idtournament, idplayer1, idplayer2, idknockoutstage, gamedate, score, idstatus, winningplayer, ngroup) 
                        values(lv_idtournament,null,p_winningplayer,lv_next_idknockoutstage,tournament_pkg.getNextGameDate(lv_idtournament,lv_gamedate), null,lv_status_match, null, lv_new_group );
                end if;
                
                
            else 
                --DBMS_OUTPUT.PUT_LINE('The record exists, so we are gonna update '||lv_exist);
                if (MOD(lv_curr_group,2) = 1) then
                    update prj_tbl_match set idplayer1 = p_winningplayer where idtournament=lv_idtournament and idknockoutstage = lv_next_idknockoutstage and ngroup = lv_new_group;
                else 
                    update prj_tbl_match set idplayer2 = p_winningplayer where idtournament=lv_idtournament and idknockoutstage = lv_next_idknockoutstage and ngroup = lv_new_group;
                end if;
                
            end if;
            
        end if;
    
    exception 
        when ex_value_champion then
            --DBMS_OUTPUT.PUT_LINE('TESTING THE CUSTOM EXCEPTION');
            DBMS_OUTPUT.PUT_LINE('WE HAVE A CHAMPION!! CONGRATULATIONS TO '|| p_winningplayer);
        
    end;
    
    


end tournament_pkg;

Create or replace trigger UPDATE_PLAYER_SCORE
after update of winningplayer on prj_tbl_match
for each row 
declare
    lv_gameswon NUMBER :=0;
begin

DBMS_OUTPUT.PUT_LINE('EXECUTING THE TRIGGER UPDATE GAMESWON OF PLAYER');
    select gameswon into lv_gameswon from prj_tbl_player where idplayer = :New.winningplayer;
    lv_gameswon := lv_gameswon+1;
    update prj_tbl_player set gameswon = lv_gameswon where idplayer = :New.winningplayer;
   
    
end;


Create or replace trigger UPDATE_CHAMPION_PLAYER
after update of winningplayer on prj_tbl_match
for each row
When (old.idknockoutstage = 4)
declare
    
begin

DBMS_OUTPUT.PUT_LINE('EXECUTING THE TRIGGER UPDATE CHAMPION PLAYER');
    
    update prj_tbl_tournament set champion = :New.winningplayer where idtournament = :New.idtournament;
   
    
end;



