USE laptops;
CREATE TABLE backup LIKE laptopcleaned;
INSERT INTO backup SELECT * FROM laptopcleaned;




ALTER TABLE laptopcleaned
CHANGE `Unnamed: 0` `index` int;

DELETE FROM laptopcleaned 
WHERE Company IS NULL AND TypeName IS NULL AND Inches IS NULL
AND ScreenResolution IS NULL AND Cpu IS NULL AND Ram IS NULL
AND Memory IS NULL AND Gpu IS NULL AND OpSys IS NULL AND
WEIGHT IS NULL AND Price IS NULL;

#ALTER TABLE laptopcleaned MODIFY COLUMN Inches DECIMAL(10,1)
UPDATE laptopcleaned l1
SET Ram = (
    SELECT REPLACE(Ram, 'GB', '')
    FROM (SELECT * FROM laptopcleaned) l2
    WHERE l2.index = l1.index
);
ALTER TABLE laptopcleaned MODIFY COLUMN Ram INTEGER;

UPDATE laptopcleaned l1
SET Weight = (
    SELECT REPLACE(Weight, 'kg', '')
    FROM (SELECT * FROM laptopcleaned) l2
    WHERE l2.index = l1.index);

ALTER TABLE laptopcleaned MODIFY COLUMN price INTEGER;

UPDATE laptopcleaned SET OpSys=
CASE
    WHEN OpSys LIKE '%mac%' THEN 'macos'
    WHEN OpSys LIKE 'windows%' THEN 'windows'
    WHEN OpSys LIKE '%linux%' THEN 'linux'
    WHEN OpSys = 'No OS' THEN 'N/A'
    ELSE 'other'
END;
 
ALTER TABLE laptopcleaned ADD COLUMN gpu_brand VARCHAR (255) AFTER Gpu, 
ADD COLUMN gpu_name VARCHAR(255) AFTER gpu_brand;

UPDATE laptopcleaned l1
SET gpu_brand = (SELECT SUBSTRING_INDEX(Gpu,' ',1) 
				FROM (SELECT * FROM laptopcleaned) l2 WHERE l2.index = l1.index);
		
UPDATE laptopcleaned l1
SET gpu_name = (SELECT REPLACE (Gpu,gpu_brand,'') FROM (SELECT * FROM laptopcleaned) l2 WHERE l2.index = l1.index);

ALTER TABLE laptopcleaned DROP COLUMN gpu;

ALTER TABLE laptopcleaned ADD COLUMN cpu_brand VARCHAR (255) AFTER Cpu, 
ADD COLUMN cpu_name VARCHAR(255) AFTER cpu_brand,
ADD COLUMN cpu_speed DECIMAL (10,1) AFTER cpu_name;

UPDATE laptopcleaned l1 SET cpu_brand = (
SELECT SUBSTRING_INDEX(Cpu,' ',1) FROM (SELECT * FROM laptopcleaned) l2 WHERE l2.index=l1.index);

UPDATE laptopcleaned l1 SET cpu_speed = (
SELECT CAST(REPLACE(SUBSTRING_INDEX(Cpu,' ',-1),'GHz','') AS DECIMAL(10,2)) FROM (SELECT * FROM laptopcleaned) l2 WHERE l2.index=l1.index);

UPDATE laptopcleaned l1 SET cpu_name = (SELECT
					REPLACE(REPLACE(Cpu,cpu_brand,''),SUBSTRING_INDEX(REPLACE(Cpu,cpu_brand,''),' ',-1),'') 
                    FROM (SELECT * FROM laptopcleaned)l2 WHERE l2.index=l1.index);
                    
ALTER TABLE laptopcleaned DROP COLUMN cpu;    

ALTER TABLE laptopcleaned ADD COLUMN resolution_width INTEGER AFTER ScreenResolution;
ALTER TABLE laptopcleaned ADD COLUMN resolution_height INTEGER AFTER resolution_width;
ALTER TABLE laptopcleaned ADD COLUMN touchscreen INTEGER AFTER resolution_height;
ALTER TABLE laptopcleaned ADD COLUMN IPS_Panel INTEGER AFTER touchscreen;
  
UPDATE laptopcleaned l1 SET resolution_width=(
SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',-1) FROM (SELECT * FROM laptopcleaned)l2 WHERE l1.index=l2.index);     

UPDATE laptopcleaned l1 SET resolution_height=(
SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',1) FROM (SELECT * FROM laptopcleaned)l2 WHERE l1.index=l2.index)          
;
UPDATE laptopcleaned l1 SET touchscreen= (
SELECT ScreenResolution LIKE '%touch%' FROM (SELECT * FROM laptopcleaned )l2 WHERE l1.index= l2.index);

UPDATE laptopcleaned l1 SET IPS_Panel= (
SELECT ScreenResolution LIKE '%IPS%' FROM (SELECT * FROM laptopcleaned )l2 WHERE l1.index= l2.index);

ALTER TABLE laptopcleaned DROP ScreenResolution;

UPDATE laptopcleaned l1 SET cpu_name = SUBSTRING_INDEX(TRIM(cpu_name),' ',2);

ALTER TABLE laptopcleaned
ADD COLUMN memory_type VARCHAR(255) AFTER Memory,
ADD COLUMN primary_storage INTEGER AFTER memory_type,
ADD COLUMN secondary_storage INTEGER AFTER primary_storage;

UPDATE laptopcleaned l1 SET memory_type =
CASE
WHEN Memory LIKE '%SSD%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    WHEN Memory LIKE '%SSD%' THEN 'SSD'
    WHEN Memory LIKE '%HDD%' THEN 'HDD'
    WHEN Memory LIKE '%Flash Storage%' THEN 'Flash Storage'
    WHEN Memory LIKE '%Hybrid%' THEN 'Hybrid'
    WHEN Memory LIKE '%Flash Storage%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    ELSE NULL
END;


UPDATE laptopcleaned
SET primary_storage = REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',1),'[0-9]+'),
secondary_storage = CASE WHEN Memory LIKE '%+%' THEN REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',-1),'[0-9]+') ELSE 0 END;

UPDATE laptopcleaned
SET primary_storage = CASE WHEN primary_storage <= 2 THEN primary_storage*1024 ELSE primary_storage END,
secondary_storage = CASE WHEN secondary_storage <= 2 THEN secondary_storage*1024 ELSE secondary_storage END;

ALTER TABLE laptopcleaned DROP COLUMN  Memory;

ALTER TABLE laptopcleaned ADD COLUMN price_bracket VARCHAR(155) AFTER price;

UPDATE laptopcleaned l1 SET price_bracket=
CASE
    WHEN price BETWEEN 0 AND 20000 THEN '10000'
    WHEN price BETWEEN 20001 AND 40000 THEN '30000'
    WHEN price BETWEEN 40001 AND 60000 THEN '50000'
    WHEN price BETWEEN 60001 AND 80000 THEN '70000'
    WHEN price BETWEEN 80001 AND 100000 THEN '90000'
    WHEN price BETWEEN 100000 AND 120000 THEN '110000'
    WHEN price BETWEEN 120001 AND 140000 THEN '130000'
END;

SELECT COUNT(Price)
FROM laptopcleaned
WHERE Price IS NULL;

SELECT Company,
SUM(CASE WHEN Touchscreen = 1 THEN 1 ELSE 0 END) AS 'Touchscreen_yes',
SUM(CASE WHEN Touchscreen = 0 THEN 1 ELSE 0 END) AS 'Touchscreen_no'
FROM laptopcleaned
GROUP BY Company;


SELECT DISTINCT cpu_brand FROM laptopcleaned;


SELECT Company,
SUM(CASE WHEN cpu_brand = 'Intel' THEN 1 ELSE 0 END) AS 'intel',
SUM(CASE WHEN cpu_brand = 'AMD' THEN 1 ELSE 0 END) AS 'amd',
SUM(CASE WHEN cpu_brand = 'Samsung' THEN 1 ELSE 0 END) AS 'samsung'
FROM laptopcleaned
GROUP BY Company;

SELECT Company,MIN(price),
MAX(price),AVG(price),STD(price)
FROM laptopcleaned
GROUP BY Company;

SELECT TypeName,COUNT(typename),AVG(ppi), AVG(price), AVg(Inches) FROM laptops.laptopcleaned
Group by TYPENAME ;
SELECT OpSys,COUNT(OpSys) FROM laptops.laptopcleaned
GROUP BY OpSys;

SELECT Company, 
       COUNT(*) AS TotalCount, 
       SUM(CASE WHEN IPS_Panel = 1 THEN 1 ELSE 0 END) AS IPS_PanelCount,
       SUM(CASE WHEN IPS_Panel = 1 THEN 1 ELSE 0 END)/COUNT(*)*100 AS AVG_IPS
FROM laptops.laptopcleaned
GROUP BY Company;


ALTER TABLE laptopcleaned ADD COLUMN ppi INTEGER;

UPDATE laptopcleaned
SET ppi = ROUND(SQRT(resolution_width*resolution_width + resolution_height*resolution_height)/Inches);

SELECT * FROM laptopcleaned
ORDER BY ppi DESC;


ALTER TABLE laptopcleaned ADD COLUMN screen_size VARCHAR(255) AFTER Inches;

UPDATE laptopcleaned
SET screen_size = 
CASE 
	WHEN Inches < 14.0 THEN 'small'
    WHEN Inches >= 14.0 AND Inches < 17.0 THEN 'medium'
	ELSE 'large'
END;

SELECT screen_size,AVG(price) FROM laptopcleaned
GROUP BY screen_size;

SELECT screen_size,AVG(ppi) FROM laptopcleaned 
GROUP BY screen_size;

SELECT Company,
SUM(CASE WHEN cpu_brand = 'Intel' THEN 1 ELSE 0 END) AS 'intel',
SUM(CASE WHEN cpu_brand = 'AMD' THEN 1 ELSE 0 END) AS 'amd',
SUM(CASE WHEN cpu_brand = 'Samsung' THEN 1 ELSE 0 END) AS 'samsung'
FROM laptopcleaned
GROUP BY Company;
