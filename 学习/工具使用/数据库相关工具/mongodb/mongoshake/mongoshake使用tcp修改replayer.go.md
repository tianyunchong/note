## 参考文档
https://www.jianshu.com/p/29522e325126

## 实现接收器和用户端的交互
```shell
func (er *ExampleReplayer) handler() {
	for msg := range er.pendingQueue {
		count := uint64(len(msg.message.RawLogs))
		if count == 0 {
			// probe request
			continue
		}

		// parse batched message
		oplogs := make([]*oplog.PartialLog, len(msg.message.RawLogs))
		for i, raw := range msg.message.RawLogs {
			oplogs[i] = new(oplog.PartialLog)
			if err := bson.Unmarshal(raw, oplogs[i]); err != nil {
				// impossible switch, need panic and exit
				LOG.Crashf("unmarshal oplog[%v] failed[%v]", raw, err)
				return
			}
			oplogs[i].RawSize = len(raw)
			//LOG.Info(oplogs[i]) // just print for test, users can modify to fulfill different needs
			LOG.Info("--------------------------\n")
			//测试连接tcp
			var tcpAddr *net.TCPAddr
			tcpAddr,_ = net.ResolveTCPAddr("tcp","127.0.0.1:9520")
			conn,err := net.DialTCP("tcp",nil,tcpAddr)
			if err!=nil {
			    LOG.Info("Client connect error ! " + err.Error())
		        return
		    }
			//reader := bufio.NewReader(conn)oplogs[i].Operation
            //msg, err := reader.ReadString()
            hexid  := ""
            if (oplogs[i].Operation == string('i')) {
                LOG.Info("插入\n")
                objid := oplog.GetKey(oplogs[i].Object, "_id")
                hexid = objid.(bson.ObjectId).Hex()
                LOG.Info(hexid)
            } else if (oplogs[i].Operation == string('u')) {
                LOG.Info("更新\n")
                obmap := oplogs[i].Query
                objid := obmap["_id"]
                hexid = objid.(bson.ObjectId).Hex()
                LOG.Info(hexid)
            } else {
                return
            }
            tmpstr := fmt.Sprintf("%s\t%s\n", oplogs[i].Operation, hexid)
            outputstr := []byte(tmpstr)
            _, err = conn.Write(outputstr)
            if err != nil {
                LOG.Info(err)
                break
            }
			conn.Close()
		}

		if callback := msg.completion; callback != nil {
			callback() // exec callback
		}

		// get the newest timestamp
		n := len(oplogs)
		lastTs := utils.TimestampToInt64(oplogs[n-1].Timestamp)
		er.Ack = lastTs

		LOG.Debug("handle ack[%v]", er.Ack)

		// add logical code below
	}
}
```