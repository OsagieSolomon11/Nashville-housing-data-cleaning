
----------------------------------------------------------------

--standardize the date format. alter the table to add a new column, then update the table with the converted date into the new column.

	ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD SaleDateConverted Date;

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET SaleDateConverted = CONVERT(date,SaleDate) 

----------------------------------------------------------------

--populate the property address data where there are null values
--the parcel id is the same and the unique id is different. i will use self join for this.

SELECT a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress, b.PropertyAddress)
FROM [Nashville Housing Data for Data Cleaning] a
JOIN [Nashville Housing Data for Data Cleaning] b
 ON a.ParcelID = b.ParcelID
 AND a.UniqueID <> b.UniqueID
 WHERE a.PropertyAddress is null

 UPDATE a
 SET PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
 FROM [Nashville Housing Data for Data Cleaning] a
JOIN [Nashville Housing Data for Data Cleaning] b
 ON a.ParcelID = b.ParcelID
 AND a.UniqueID <> b.UniqueID
 WHERE a.PropertyAddress is null

 ----------------------------------------------------------------

 --breaking out address into individual columns (address, city, state)

SELECT
SUBSTRING(PropertyAddress, 1, CHARINDEX(',' , PropertyAddress) -1 ) as Address
,SUBSTRING(PropertyAddress, CHARINDEX(',' , PropertyAddress) +1, LEN(PropertyAddress)) as Address
FROM [Nashville Housing Data for Data Cleaning]

ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD PropertySplitAddress Nvarchar(255);

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET PropertySplitAddress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',' , PropertyAddress) -1 )


	ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD PropertySplitCity Nvarchar(255);

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',' , PropertyAddress) +1, LEN(PropertyAddress))

	select *
	FROM [Nashville Housing Data for Data Cleaning]

----------------------------------------------------------------
--spliting owners address. we can use PARSENAME query here instead of SUBSTRINGS command. PARSENAME is more concise. 
--PARSENAME  works backward unlike SUBSTRINGS

SELECT 
PARSENAME(REPLACE(OwnerAddress, ',', '.') , 3) 
,PARSENAME(REPLACE(OwnerAddress, ',', '.') , 2)
,PARSENAME(REPLACE(OwnerAddress, ',', '.') , 1)
FROM [Nashville Housing Data for Data Cleaning]

ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD OwnerSplitAddress Nvarchar(255);

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 3) 

	ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD OwnerSplitCity Nvarchar(255);

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 2)

	ALTER TABLE [Nashville Housing Data for Data Cleaning]
	ADD OwnerSplitState Nvarchar (255);

	UPDATE [Nashville Housing Data for Data Cleaning]
	SET OwnerSplitState = PARSENAME(REPLACE(OwnerAddress, ',', '.') , 1)

------------------------------------------------------------------

--change 'Y' and 'N' to 'Yes' and 'No' in "SoldAsVacant" field

SELECT SoldAsVacant
, CASE WHEN SoldAsVacant = 'Y' THEN 'Yes'
       WHEN SoldAsVacant = 'N' THEN 'No'
	   ELSE SoldAsVacant
	   END
FROM [Nashville Housing Data for Data Cleaning]

UPDATE [Nashville Housing Data for Data Cleaning]
SET SoldAsVacant = CASE WHEN SoldAsVacant = 'Y' THEN 'Yes'
       WHEN SoldAsVacant = 'N' THEN 'No'
	   ELSE SoldAsVacant
	   END
------------------------------------------------------------------
