# This is a repository of MongoDB Zenclass programme's solution

# MongoDB queries for each questions

1. Find all the topics and tasks which are thought in the month of October

```bash
db.getCollection('Topics').aggregate(
  [
    {
      $lookup: {
        from: 'Tasks',
        localField: 'topic_id',
        foreignField: 'topic_id',
        as: 'taskinfo'
      }
    },
    {
      $match: {
        $and: [
          {
            $or: [
              {
                topic_date: {
                  $gt: ISODate(
                    '2020-09-30T00:00:00.000Z'
                  )
                }
              },
              {
                topic_date: {
                  $lt: ISODate(
                    '2020-11-01T00:00:00.000Z'
                  )
                }
              }
            ]
          },
          {
            $or: [
              {
                'taskinfo.due_date': {
                  $gt: ISODate(
                    '2020-09-30T00:00:00.000Z'
                  )
                }
              },
              {
                'taskinfo.due_date': {
                  $lt: ISODate(
                    '2020-11-01T00:00:00.000Z'
                  )
                }
              }
            ]
          }
        ]
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```

2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020

```bash
db.getCollection('Company_Drives').aggregate(
  [
    {
      $match: {
        drive_date: {
          $gte: ISODate(
            '2020-10-15T18:30:00.000Z'
          ),
          $lte: ISODate(
            '2020-10-31T18:30:00.000Z'
          )
        }
      }
    },
    { $project: { _id: 0, user_id: 0 } }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```

3. Find all the company drives and students who are appeared for the placement.

```bash
db.getCollection('Company_Drives').aggregate(
  [
    {
      $lookup: {
        from: 'Users',
        localField: 'user_id',
        foreignField: 'user_id',
        as: 'userinfo'
      }
    },
    {
      $project: {
        _id: 0,
        'userinfo.name': 1,
        company: 1,
        drive_date: 1,
        'userinfo.email': 1,
        'userinfo.user_id': 1
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```

4. Find the number of problems solved by the user in codekata

```bash
db.getCollection('Codekata').aggregate(
  [
    {
      $lookup: {
        from: 'Users',
        localField: 'user_id',
        foreignField: 'user_id',
        as: 'userinfo'
      }
    },
    {
      $project: {
        _id: 0,
        user_id: 1,
        problems: 1,
        'userinfo.name': 1
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```

5. Find all the mentors with who has the mentee's count more than 15

```bash
db.getCollection('Mentors').aggregate(
  [
    { $match: { mentee_count: { $gte: 15 } } },
    {
      $project: {
        _id: 0,
        mentor_id: 0,
        mentee_count: 0
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```

6. Find the number of users who are absent and task is not submitted between 15 oct-2020 and 31-oct-2020

```bash
db.getCollection('Attendance').aggregate(
  [
    {
      $lookup: {
        from: 'Topics',
        localField: 'topic_id',
        foreignField: 'topic_id',
        as: 'Topics'
      }
    },
    {
      $lookup: {
        from: 'Tasks',
        localField: 'topic_id',
        foreignField: 'topic_id',
        as: 'Tasks'
      }
    },
    {
      $match: {
        $and: [
          { attended: false },
          { 'Tasks.submitted': false }
        ]
      }
    },
    {
      $match: {
        $and: [
          {
            $or: [
              {
                'Topics.topic_date': {
                  $gte: ISODate(
                    '2020-10-15T00:00:00.000Z'
                  )
                }
              },
              {
                'Topics.topic_date': {
                  $lte: ISODate(
                    '2020-10-31T00:00:00.000Z'
                  )
                }
              }
            ]
          },
          {
            $or: [
              {
                'Tasks.due_date': {
                  $gte: ISODate(
                    '2020-10-15T00:00:00.000Z'
                  )
                }
              },
              {
                'Tasks.due_date': {
                  $lte: ISODate(
                    '2020-10-31T00:00:00.000Z'
                  )
                }
              }
            ]
          }
        ]
      }
    },
    { $count: 'No_of_students_absent' }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
