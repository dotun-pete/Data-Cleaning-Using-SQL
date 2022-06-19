# Data-Cleaning-Using-SQL

--cleaning data
select *
from [dbo].[NashvilleHousing]

--standardize Date Format

 select SaleDateConverted, convert(Date,SaleDate)
 from [dbo].[NashvilleHousing]

alter table [dbo].[NashvilleHousing]
add SaleDateConverted Date;

update NashvilleHousing
set SaleDateConverted = Convert(Date,SaleDate)

--Populate Property Address data
select *
from [dbo].[NashvilleHousing]
--where PropertyAddress IS NULL
order by ParcelID

select a.ParcelID,a.PropertyAddress,b.ParcelID,b.PropertyAddress,isnull(a.PropertyAddress,b.PropertyAddress)
from [dbo].[NashvilleHousing] a
join [dbo].[NashvilleHousing] b
   on a.ParcelID =b.ParcelID
   AND a.[UniqueID ] <> b.[UniqueID ]
where a.PropertyAddress is null

UPDATE a
SET PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
from [dbo].[NashvilleHousing] a
join [dbo].[NashvilleHousing] b
   on a.ParcelID =b.ParcelID
   AND a.[UniqueID ] <> b.[UniqueID ]
where a.PropertyAddress is null

--Breaking out Address into Indiviual Columns (Address,City,State)

select PropertyAddress 
from [dbo].[NashvilleHousing]

select 
substring(PropertyAddress,1,charindex(',',PropertyAddress)-1) as Address,
substring(PropertyAddress,charindex(',',PropertyAddress)+1,len(PropertyAddress)) as Address
from [dbo].[NashvilleHousing]

alter table [dbo].[NashvilleHousing]
add PropertySplitAddress Nvarchar(255);

update NashvilleHousing
set PropertySplitAddress = substring(PropertyAddress,1,charindex(',',PropertyAddress)-1)

alter table [dbo].[NashvilleHousing]
add PropertySplitCity Nvarchar(255);

update NashvilleHousing
set PropertySplitCity = substring(PropertyAddress,charindex(',',PropertyAddress)+1,len(PropertyAddress))


--split ownerAddress 

 select parsename(replace(owneraddress,',','.'),3),
		parsename(replace(owneraddress,',','.'),2),
		parsename(replace(owneraddress,',','.'),1)
 from NashvilleHousing

 
alter table [dbo].[NashvilleHousing]
add OwnerSplitAddress Nvarchar(255);
update NashvilleHousing
set OwnerSplitAddress = parsename(replace(owneraddress,',','.'),3)

alter table [dbo].[NashvilleHousing]
add OwnerSplitCity Nvarchar(255);
update NashvilleHousing
set OwnerSplitCity = parsename(replace(owneraddress,',','.'),2)

alter table [dbo].[NashvilleHousing]
add OwnerSplitState Nvarchar(255);
update NashvilleHousing
set OwnerSplitState = parsename(replace(owneraddress,',','.'),1)


select *
from NashvilleHousing


--update sold as vacant column
select distinct(soldasvacant), count(soldasvacant)
from NashvilleHousing
group by SoldAsVacant


select soldasvacant,
case when SoldAsVacant = 'Y' then 'Yes'
     when SoldAsVacant = 'N' then 'No'
     else SoldAsVacant
	 end
from NashvilleHousing

update NashvilleHousing
set SoldAsVacant = case when SoldAsVacant = 'Y' then 'Yes'
						 when SoldAsVacant = 'N' then 'No'
						else SoldAsVacant
						end


--DROP COLUMNS

alter table NashvilleHousing
drop column OwnerAddress, TaxDistrict, PropertyAddress


alter table NashvilleHousing
drop column saledate

select*
from NashvilleHousing
