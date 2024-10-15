# Python-Tips

**Tips and Quick Copy of Frequently Used Codes**


## 1. Pandas setting
**Set number of rows and columns to display.**
```
pd.set_option('display.max_columns', 50)
pd.set_option('display.max_rows', 50)
```


## 2. Concatenate multiple files in one
**Regex and match for naming to identify which file the record comes from.**
```
files = glob.glob(r"directory\*.xlsx")

df = pd.DataFrame()
for file in files:
    file_name = os.path.basename(file)
    data = pd.read_excel(file, index_col= None)
    match = re.search(r'(\d+)\.(\d+)\.(\d+)', file_name)
    if match:
        df['Date_In'] = ''.join(match.groups())
        df['Date_In'] = pd.to_datetime(df['Date_In'], format='%m%d%Y')
    df = pd.concat([df, data])
```


## 3. Move last columns to front
```
cols = exception.columns.tolist()
cols = cols[-4:] + cols[:-4]
exception = exception[cols]
```

## 4. Pandas String Modification
**For simple string output**
```
name = "Ted Jung"
"I am {} and I am Korean".format(name)
```

**For variable input as string**
```
for month in months:
key = f"result_{month}"
```

## 5. Value Flatten in Single Axis
**If 'Condition_X' is in either of 'Condition_A', 'Condition_B' or 'Condition_C', then True.**
```
df[df['Account' == 'A']['Condition_X'].isin(
    df2[df2['Account'] == 'A'][['Condition_A', 'Condition_B', 'Condition_C']].values.flatten()
    )].any()
```

## 6. Datetime.time tips
**If 'time' is less than 8am, then True**
```
df[df['time'].apply(lambda x: x.time()) < datetime.time(hour = 8, minute = 00)]
```

**Value as 8 days**
```
pd.timedelta(8, 'd')
```

**First day of month**
```
df['date'].to_numpy().astype('datetime64[M]')
```

**Converting string to datetime but have multiple formats.**
```
def date_conversion(date_str):
    for fmt in ('%m-%d-%Y', '%Y%m%d'):
        try:
            return pd.to_datetime(date_str, format=fmt)
        except ValueError:
            continue
    return

df['date'] = df['date'].apply(date_conversion)
```


## 7. If loc --> np.where
**Replace 'If Loc' logic to np.where. Faster and much efficient.**
```
if df[df[conditions]]:
    df['c_exception'] = 'T'
else:
    df['c_exception'] = 'F'

mask = condition
df['c_exception'] = np.where(mask, 'T', 'F')
```


## 8. Groupby 
**Simple display of Group**
```
list = df.groupby(['Account', 'Service Request']).size().reset_index()
```
**Group with multiple factors**
```
list = df.groupby(['Account', 'Service Request']).agg(
    quantity = ('Dollar Amount', 'size'),
    sum = ('Dollar Amount', 'sum')
).reset_index()
```

## 9. Float to STR astype() remove .0
```
df['Account'] = df['Account'].astype(str)
df['Account'] = df['Account'].apply(lambda x: x.split('.')[0])
```

## 10. List find condition in
**If list is [A,B] format.**
```
any(x == 'Condition_A' for x,y in list)
```

## 11. Export Multiple Sheets with Highlights
**Highlight Columns**
```
highlight_cols = [
    'A', 'B', 'C', 'D', 'E'
]

def highlight_columns(x):
    df_styler = pd.DataFrame('', index = x.index, columns = x.columns)
    df_styler.loc[:, highlight_cols] = 'background-color: yellow'
    return df_styler

df1_styled = df1.style.apply(highlight_columns, axis=None)
df2_styled = df2.style.apply(highlight_columns, axis=None)
```

**Export in .xlsx**
```
with pd.ExcelWriter(r"directory\result.xlsx") as writer:
    df1_styled.to_excel(writer, sheet_name = 'Result_1', index=False)
    df2_styled.to_excel(writer, sheet_name = 'Result_2', index=False)

print(pd.Timestamp.today())
print('df1', len(df1))
print('df2', 'len(df2))
```