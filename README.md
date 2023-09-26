create table if not exists Genre (
	id SERIAL primary key,
	Name varchar(40) not null
);

create table if not exists Executor(
	id SERIAL primary key,
	pseudonym varchar(40),
	genre_executor text
);

create table if not exists GenreExecutor (
	Genre_id integer references Genre(id),
	Executor_id integer references Executor(id),
	constraint pk primary key (Genre_id, Executor_id)
);

create table if not exists Album (
	id SERIAL primary key,
	Name varchar(40) not null,
	year_of_issue date
);

create table if not exists ExecutorAlbum (
	Album_id integer references Album(id),
	Executor_id integer references Executor(id),
	constraint ps primary key (Album_id, Executor_id)
);

create table if not exists Treck (
	id SERIAL primary key,
	Album_id integer references Album(id),
	Name varchar(40) not null,
	track_duration text
);

create table if not exists Collection (
	id SERIAL primary key,
	tracks varchar(80) not null,
	year_of_issue date
);

create table if not exists TreckCollection (
	Treck_id integer references Treck(id),
	Collection_id integer references Collection(id),
	constraint pc primary key (Treck_id, Collection_id)
);

insert into Genre
values(1, 'Country'),(2, 'Jazz'),(3, 'Hip-hop');

insert into Executor
values(1, 'Eminem', 'Hip-hop'),
	(2, 'Ludacris', 'Hip-hop'),
	(3, 'Louis Armstrong', 'Jazz'), 
	(4, 'Willie Nelson', 'Country');

insert into GenreExecutor (genre_id, executor_id)
values('1', '4'),('3', '1'),('3', '2'),('2', '3');

insert into Album
values(1, 'The Marshall Mathers LP 2', '2013-11-05'),
	(2, 'Chicken-n-Beer', '2003-10-07'),
	(3, 'Ella and Louis Again', '1957-08-13'),
	(4, 'Always On My Mind', '1972-10-31');
	
insert into ExecutorAlbum (Album_id, Executor_id)
values('1', '1'),('2', '2'),('3', '3'),('4', '4');

insert into Treck
values(1, '1', 'Beautiful Pain', '4:27'),
	(2, '1', 'The Monster', '5:18'),
	(3, '2', 'We Got', '4:21'),
	(4, '3', 'Love Is Here to Stay', '4:00'),
	(5, '3', 'I,ve Got My Love to Keep Me Warm', '3:12'),
	(6, '4', 'Old Fords & A Natural Stone', '2:31');

insert into Collection
values(1, 'Curtain Call 2', '2022-08-05'),
	(2, 'Disturbing tha Peace', '2005-12-013'),
	(3, 'Ella and Louis Again', '1957-08-13'),
	(4, 'Always On My Mind', '1972-10-31');

insert into TreckCollection (Treck_id, Collection_id)
values('1', '1'),('2', '2'),('3', '3'),('4', '4');

select Name, track_duration from Treck
where track_duration = (select MAX(track_duration) from Treck)

select Name, track_duration from Treck
where track_duration >= '3:50';

select tracks from Collection
where (year_of_issue between  '2018-01-01' and  '2022-12-31');

select pseudonym from Executor
where pseudonym like '%';

select 	Name from Treck
where 	Name like '%My%';

select 	genre_executor, count(*) from Executor
group by genre_executor
order by count(*) desc;

select count(*) from Album A
left join Treck T on A.id = T.album_id
where Name (select A.year_of_issue between  '2013-01-01' and  '2020-12-31' from A);

select AVG(track_duration) from Treck;

select DISTINCT	pseudonym, year_of_issue from Executor E
Full join ExecutorAlbum EA on EA.Executor_id = E.id
left join Album A on EA.Album_id = A.id
where E.pseudonym not in(select A.year_of_issue between  '2020-01-01' and  '2020-12-31' from album)
GROUP BY E.pseudonym, A.year_of_issue;

select DISTINCT	tracks, pseudonym from Collection C
join TreckCollection TC on TC.Collection_id = C.id
join Treck T on T.id = TC.treck_id 
join Album A on A.id = T.album_id
join ExecutorAlbum EA on EA.album_id = A.id
join Executor E on E.id = EA.executor_id
where pseudonym like 'Eminem';