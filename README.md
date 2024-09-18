db.users.aggregate([
  {
    $lookup: {
      from: 'posts',
      localField: '_id',
      foreignField: 'authorId',
      as: 'userPosts',
    },
  },
  {
    $lookup: {
      from: 'comments',
      localField: '_id',
      foreignField: 'userId',
      as: 'userComments',
    },
  },
  {
    $lookup: {
      from: 'likes',
      localField: '_id',
      foreignField: 'userId',
      as: 'userLikes',
    },
  },
  {
    $lookup: {
      from: 'views',
      localField: '_id',
      foreignField: 'userId',
      as: 'userViews',
    },
  },
  {
    $project: {
      postsCount: { $size: '$userPosts' },
      commentsCount: { $size: '$userComments' },
      likesCount: { $size: '$userLikes' },
      viewsCount: { $size: '$userViews' },
      ageRange: {
        $cond: [
          { $and: [{ $gte: ['$age', 18] }, { $lt: ['$age', 25] }] },
          '18-24',
          {
            $cond: [
              { $and: [{ $gte: ['$age', 25] }, { $lt: ['$age', 35] }] },
              '25-34',
              {
                $cond: [
                  { $and: [{ $gte: ['$age', 35] }, { $lt: ['$age', 45] }] },
                  '35-44',
                  '45+',
                ],
              },
            ],
          },
        ],
      },
    },
  },
  {
    $group: {
      _id: '$ageRange',
      totalPosts: { $sum: '$postsCount' },
      totalComments: { $sum: '$commentsCount' },
      totalLikes: { $sum: '$likesCount' },
      totalViews: { $sum: '$viewsCount' },
    },
  },
]);
