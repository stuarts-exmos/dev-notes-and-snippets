```c#
SqlParameter outputTotalRecordCount = new SqlParameter("@intTotalRecordCount", SqlDbType.Int)
                    {
                        Direction = ParameterDirection.Output
                    };

sqlCommand.Parameters.Add(outputTotalRecordCount);
```
