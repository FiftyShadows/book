####可能很多理解上的错误,尽量只贴代码,用的是pymongo
#####一张表类似excel的一个sheet
import pymongo

client = pymongo.MongoClient('localhost',27017)

walden = client['walden']#给数据库名称,相当与一个excel文件的文件名 左边的是python的对象
sheet_tab = walden['sheet_tab']#一个表
#plugins brows repositorles  mongo  mongo plugin
# path = '/Users/Hou/Desktop/walden.txt'
# with open(path,'r') as f:
#     lines = f.readlines()
#     for index,line in enumerate(lines):
#         data = {
#             'index':index,
#             'line' :line,
#             'words':len(line.split())
#         }
#         sheet_tab.insert_one(data)

# $lt/$lte/$gt/$gte/$ne，依次等价于</<=/>/>=/!=。（l表示less g表示greater e表示equal n表示not  ）
for item in sheet_tab.find({'words':{'$lt':5}}):
    print(item)
