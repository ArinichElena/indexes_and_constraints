# indexes_and_constraints
Индексы и ограничения
--referral_doctor - факт направления к врачу
create table referral_doctor (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
specialization_id INT not null references specialization,
load_date TIMESTAMP default CURRENT_TIMESTAMP 
);
--record_doctor - факт записи к врачу
create table record_doctor (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
referal_id BIGINT references referal,
doctor_id INT not null references doctor,
clinic_id INT not null references clinic,
load_date TIMESTAMP default CURRENT_TIMESTAMP,
record_date TIMESTAMP not null
);
--reception_doctor - факт состоявшегося приема у врача
create table reception_doctor (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
record_id BIGINT references record,
doctor_id INT not null references doctor,
clinic_id INT not null references clinic,
load_date TIMESTAMP default CURRENT_TIMESTAMP,
code_diag VARCHAR(32),
comment_doctor TEXT
); 
--простой индекс по полю code_diag, для таблицы reception_doctor
--группировать/отбирать данные по коду диагноза
create index idx_reception_diag
on reception_doctor(code_diag);
--простой индекс по дате приема, для таблицы reception_doctor
--составление графиков по дням, для отслеживания количества приемов
--отбор только необходимого периода
create index idx_recep_doc_load_date
on reception_doctor(load_date);
--referral_research - факт направления на исследование
create table referral_research (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
research_id INT not null references research,
load_date TIMESTAMP default CURRENT_TIMESTAMP
);
--record_research - факт записи на исследование
create table record_research (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
referal_research_id BIGINT references referal_research,
research_id INT not null references research,
clinic_id INT not null references clinic,
load_date TIMESTAMP default CURRENT_TIMESTAMP,
record_date TIMESTAMP not null
);
--fact_research - факт состоявшегося исследования
create table fact_research (
id BIGSERIAL primary key,
patient_id BIGINT not null references patient,
record_research_id BIGINT references record_research,
doctor_id INT not null references doctor,
clinic_id INT not null references clinic,
laboratory_id INT references laboratory,
research_id INT not null references research,
load_date TIMESTAMP default CURRENT_TIMESTAMP,
result_date TIMESTAMP not null,
result_research TEXT,
comment_research TEXT
);
--простой индекс по дате исследования, для таблицы fact_research
--составление графиков по дням, для отслеживания количества исследований
--отбор только необходимого периода
create index idx_fact_research_load_date
on fact_research(load_date);
--patient - пациент
create table patient (
id BIGSERIAL primary key,
surname VARCHAR(100) not null,
name VARCHAR(100) not null,
patronymic VARCHAR(100),
birthday DATE,
medical_policy BIGINT UNIQUE,
gender VARCHAR(32)
);
--clinic - поликлиника
create table clinic (
id SERIAL primary key,
name TEXT not null,
short_name TEXT not null,
address TEXT not null,
type_clinic_id INT not null references type_clinic
);
--type_clinic – тип поликлиники
create table type_clinic (
id SERIAL primary key,
name VARCHAR(32) not null
);
--laboratory - лаборатория
create table laboratory (
id SERIAL primary key,
name TEXT not null,
short_name TEXT not null,
address TEXT not null,
clinic_id INT not null references clinic
);
--doctor - врач
create table doctor (
id SERIAL primary key,
surname VARCHAR(100) not null,
name VARCHAR(100) not null,
patronymic VARCHAR(100),
birthday DATE,
gender VARCHAR(32),
specialization_id INT not null references specialization,
clinic_id INT not null
);
--specialization – специализация врача
create table specialization (
id SERIAL primary key,
name VARCHAR(200) not null
);
--research - исследование
create table research (
id SERIAL primary key,
name TEXT not null,
short_name TEXT not null,
type_research_id INT not null references type_research
);
--type_research – тип исследования
create table type_research (
id SERIAL primary key,
name VARCHAR(32) not null
);
